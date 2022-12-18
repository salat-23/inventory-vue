<script setup>
import {ref, onMounted} from "vue";
import SideBar from "./SideBar.vue"
import BottomModal from "./BottomModal.vue";
import ItemsGrid from "./ItemsGrid.vue";


</script>

<template>
  <div class="inventory">
    <ItemsGrid class="grid"/>

  </div>
</template>

<style scoped lang="scss">
// to be honest, I am done with this layout... It just won't place itself like I want it to. Idk why it behaves so stupid and how do I actually make it.
// side bar and bottom thing are made but pretty much useless at this point.
// enjoy just the grid
.inventory {
  width: 80vw;
  height: 80vh;
  & > div {
    margin: 0 auto;
    transform: translate(-150px, -20px);
  }
}
</style>
