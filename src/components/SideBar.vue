<script setup>
import MockText from "./MockText.vue";
import GenericContainer from "./GenericContainer.vue";
import sidebarImg from "../assets/sidebarimg.png"
</script>

<template>
  <GenericContainer>
    <div class="sidebar">
      <div class="sidebar_image">
        <img :src="sidebarImg">
      </div>
      <div class="sidebar_text">
        <MockText width="230px" height="30px"></MockText>
        <MockText width="190px" height="10px"></MockText>
        <MockText width="210px" height="10px"></MockText>
        <MockText width="190px" height="10px"></MockText>
        <MockText width="180px" height="10px"></MockText>
        <MockText width="160px" height="10px"></MockText>
        <MockText width="90px" height="10px"></MockText>
      </div>
    </div>
  </GenericContainer>
</template>

<style scoped lang="scss">
.sidebar {
  .sidebar_image {
    img {
      width: 100%;
    }
  }

  .sidebar_text {
    margin-top: 20px;
    display: flex;
    flex-direction: column;
    gap: 25px;
  }
}
</style>