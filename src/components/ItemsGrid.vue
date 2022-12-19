<script setup>
import {computed, onBeforeMount, onMounted, reactive, ref, toRaw} from 'vue'
import close from "../assets/close.png"

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
      0: {id: 1, type: "green", amount: 3, position: 0},
      1: {id: 2, type: "red", amount: 1, position: 1},
      3: {id: 3, type: "blue", amount: 2, position: 3},
      4: {id: 4, type: "red", amount: 7, position: 4}
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

const onClick = function (event, item) {
  itemSelected.value = true;
  for (let field in item) {
    selectedItem[field] = item[field];
  }
}

const onEmptyClick = function (event) {
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
  // this.set(items[handItemPosition], "position", item.position);
  // this.set(items[item.position], "position", handItemPosition);
  // items[handItemPosition].position = handItemPosition;
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
  console.log(`moved ${handItemPosition} => ${item.position}`)
  updateListTrigger.value++;
}

</script>

<template>
  <div class="grid">
    <div :class="'item_description ' + sideStyle">
      <div class="close" @click="onEmptyClick($event)"><img :src="close"></div>
      <h3>id: {{ selectedItem.id }}</h3>
      <p>type: {{ selectedItem.type }}</p>
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
               @click="onEmptyClick($event)">
          </div>
          <div class="amount" v-if="item.type!=='empty'">
            {{ item.amount }}
          </div>
        </td>
      </tr>
    </table>
  </div>
</template>

<style scoped lang="scss">
.grid {
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

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  cursor: pointer;
  z-index: 10;
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