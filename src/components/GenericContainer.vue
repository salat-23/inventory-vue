<script setup>

</script>

<template>
  <div class="container">
    <slot />
  </div>
</template>

<style scoped lang="scss">
  .container {
    padding: 20px;
    background: #262626;
    border: 1px solid #4D4D4D;
    border-radius: 14px;
    width: max-content;
  }
</style>