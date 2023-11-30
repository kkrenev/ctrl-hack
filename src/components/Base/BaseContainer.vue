<template>
  <div class="container">
    <slot name="default"></slot>
  </div>
</template>
<script>
export default {
  name: 'BaseContainer'
}
</script>

<style scoped>
.container {
  padding: 10px;
  background-color: #fff;
  min-height: 300px;
  border-radius: 8px;
  border: 1px solid #000;
}
</style>
