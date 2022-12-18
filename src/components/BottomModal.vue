<script setup>

import GenericContainer from "./GenericContainer.vue";
import MockText from "./MockText.vue";
import close from "../assets/close.png"
</script>

<template>
  <GenericContainer>
    <div class="content">
      <MockText width="52vw" height="40px"/>
      <div class="close_container">
        <img :src="close">
      </div>
    </div>
  </GenericContainer>
</template>

<style scoped lang="scss">
.content {
  display: flex;
  gap: 10px;

  .close_container {
    cursor: pointer;

    img {
      top: -15px;
      right: -8px;
    }
  }


}
</style>