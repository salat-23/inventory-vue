<script setup>
import {computed, reactive, ref} from 'vue'

const inventorySize = 25;
const width = 5;
let updateListTrigger = ref(true);
// hash of items - position: object
const items = reactive({
  0: {id: 1, type: "green", amount: 3, position: 0},
  1: {id: 2, type: "red", amount: 1, position: 1},
  3: {id: 3, type: "blue", amount: 2, position: 3},
  4: {id: 4, type: "red", amount: 7, position: 4}
});
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
    <table v-if="updateListTrigger">
      <tr v-for="row in rows">
        <td @drop="onDrop($event, item)" @dragover.prevent @dragenter.prevent class="item_cell" v-for="item in row">
          <div
              v-if="item"
              draggable="true"
              :class="'item '+item.type"
              @dragstart="startDrag($event, item)">
          </div>
          <div v-else>
            e
          </div>
        </td>
      </tr>
    </table>
  </div>
</template>

<style scoped lang="scss">
.grid {
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

.item_cell {
  padding: 30px;
  outline: 3px solid #4D4D4D;
}

.item {
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