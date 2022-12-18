<script setup>
import {ref, onMounted} from "vue";
import SideBar from "./SideBar.vue"
import BottomModal from "./BottomModal.vue";
import ItemsGrid from "./ItemsGrid.vue";


</script>

<template>
  <div class="inventory">
    <SideBar />
    <ItemsGrid />
    <BottomModal />
  </div>

</template>

<style scoped>
  .inventory {
    width: 1000px;
  }
</style>
