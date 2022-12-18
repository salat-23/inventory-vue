<script setup>
  import {computed} from "vue";

  const props = defineProps({
    width: String,
    height: String
  })

  const cssProperties = computed(() => {
    return {
      "width": props.width,
      "height": props.height
    }
  })
</script>

<template>
  <div :style="cssProperties"></div>
</template>

<style scoped>
  div {
    margin: 0 auto;
    background: linear-gradient(90deg, #3C3C3C 0%, #444444 51.04%, #333333 100%);
    border-radius: 8px;
  }
</style>