<script setup>
import {computed, onBeforeMount, onMounted, reactive, ref, toRaw} from 'vue'
import close from "../assets/close.png"

const itemTypes = [
  "green",
  "blue",
  "red"
]
const inventorySize = 25;
const width = 5;
let updateListTrigger = ref(true);
let itemSelected = ref(false);
let selectedItem = reactive({});
// hash of items - position: object
let items;
const mounted = onBeforeMount(function () {
  if (localStorage.getItem("items")) {
    items = reactive(JSON.parse(localStorage.getItem("items")))
  } else {
    items = reactive({
      0: {type: "green", amount: 3, position: 0},
      1: {type: "red", amount: 1, position: 1},
      3: {type: "blue", amount: 2, position: 3},
      4: {type: "red", amount: 7, position: 4}
    })
  }
});
setInterval(() => {
  let savedItems = toRaw(items);
  localStorage.setItem("items", JSON.stringify(savedItems));
}, 500)
const sideStyle = computed(() => {
  return itemSelected.value ? "" : "hidden";
})
const rows = computed(() => {
  const rows = [];
  let columns = [];
  for (let i = 0; i < inventorySize; i++) {
    if (items[i]) {
      let item = items[i];
      columns.push(item);
    } else {
      let item = {type: "empty", position: i};
      columns.push(item);
    }
    if ((i + 1) % width === 0) {
      rows.push(columns);
      columns = []
    }
  }
  return rows;
});

const deleteAll = function () {
  for (let index in items) {
    items[index] = undefined;
  }
}

const deleteItem = function (item) {
  itemSelected.value = false;
  items[item.position] = undefined;
}

const addItem = function (event, item) {
  event.preventDefault();
  const randomType = Math.floor(Math.random() * 3);
  console.log(randomType)
  items[item.position] = {type: itemTypes[randomType], position: item.position, amount: 1};
}

const setAmount = function (event, item, amount) {
  if (amount <= 0) return;
  items[item.position].amount = amount;
  selectedItem.amount = amount;
}

const onClick = function (event, item) {
  itemSelected.value = true;
  for (let field in item) {
    selectedItem[field] = item[field];
  }
}

const onEmptyClick = function (event) {
  console.log("empty clicked")
  itemSelected.value = false;
  for (let field in selectedItem) {
    selectedItem[field] = undefined;
  }
}

const startDrag = (event, item) => {
  event.dataTransfer.dropEffect = "move";
  event.dataTransfer.effectAllowed = "move";
  event.dataTransfer.setData("item_position", item.position);
}

const onDrop = function (event, item) {
  const handItemPosition = event.dataTransfer.getData("item_position");
  if (item.type === "empty") {
    let swap = items[handItemPosition];
    swap.position = item.position;
    items[handItemPosition] = undefined;
    items[item.position] = swap;
  } else {
    let firstSwapIndex = item.position;
    let secondSwapIndex = handItemPosition;
    let swap1 = items[item.position];
    let swap2 = items[handItemPosition];
    let temp = swap1.position;
    swap1.position = swap2.position;
    swap2.position = temp;
    items[firstSwapIndex] = swap2;
    items[secondSwapIndex] = swap1;
  }
  updateListTrigger.value++;
}

</script>

<template>
  <div tabindex="0" @keydown.esc="onEmptyClick($event)" class="grid">
    <div :class="'item_description ' + sideStyle">
      <div class="close" @click="onEmptyClick($event)"><img :src="close"></div>
      <div class="item_container">
        <div :class="'item ' + selectedItem.type"></div>
      </div>
      <div class="controls_container">
        <h3>{{ selectedItem.type }}</h3>
        <p>amount: {{ selectedItem.amount }}</p>
        <span class="buttons_container">
          <button @click="setAmount($event, selectedItem, selectedItem.amount-1)">-</button>
          <button @click="setAmount($event, selectedItem, selectedItem.amount+1)">+</button></span>
        <button @click="deleteItem(selectedItem)">Delete</button>
      </div>
    </div>
    <table v-if="updateListTrigger">
      <tr v-for="row in rows">
        <td @drop="onDrop($event, item)" @dragover.prevent @dragenter.prevent class="item_cell" v-for="item in row">
          <div
              v-if="item.type!=='empty'"
              draggable="true"
              :class="'item '+item.type"
              @click="onClick($event, item)"
              @dragstart="startDrag($event, item)">
          </div>
          <div v-else class="item empty"
               draggable="false"
               @click="onEmptyClick($event)"
               @contextmenu="addItem($event, item)">
          </div>
          <div class="amount" v-if="item.type!=='empty'">
            {{ item.amount }}
          </div>
        </td>
      </tr>
    </table>
    <div class="bottom_controls">
      <button @click="deleteAll()">Delete all</button>
    </div>
  </div>
</template>

<style scoped lang="scss">
.grid {
  outline: none;
  position: relative;
  width: 50%;
  overflow: hidden;
  background: #262626;
  border: 2px solid #4D4D4D;
  border-radius: 12px;

  table {
    width: 100%;
    height: 100%;
  }
}

.bottom_controls {
  padding: 10px;
  width: 100%;
  align-items: center;
  display: flex;
  flex-direction: column;
}

.buttons_container {
  display: flex;

  & > * {
    width: 100px;
  }
}

button {
  cursor: pointer;
  padding: 10px;
  width: 90%;
  background: #FF8888;
  border: 1px solid #4D4D4D;
  font-size: 24px;
  border-radius: 8px;
  color: white;
  font-weight: 700;
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  cursor: pointer;
  z-index: 10;
}


.item_container {
  padding: 50px;
}

.controls_container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.item_description {
  right: 0;
  z-index: 2;
  position: absolute;
  height: 100%;
  width: 44%;
  background: red;

  background: rgba(38, 38, 38, 0.5);
  /* Primary Border */

  border-left: 1px solid #4D4D4D;
  backdrop-filter: blur(100px);

  transition: all 0.3s;

  &.hidden {
    transform: translateX(100%);
  }
}

.item_cell {
  padding: 30px;
  outline: 3px solid #4D4D4D;
  position: relative;
}

.amount {
  position: absolute;
  right: 0;
  bottom: 0;
  border: 1px solid #4D4D4D;
  width: 20px;
  border-radius: 10px 0 0 0;
  text-align: center;
}

.item {
  cursor: pointer;
  width: 100%;
  height: 100%;
  aspect-ratio: 1/1;

  &::before {
    content: "";
    width: 100%;
    height: 100%;
    display: inline-block;
    transform: translate(10px, -10px);
    backdrop-filter: blur(100px);
  }
}

.empty {
  cursor: default;
}

.green {
  background: green;
}

.red {
  background: red;
}

.blue {
  background: blue;
}
</style>