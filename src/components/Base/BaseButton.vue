<template>
  <div>
    <label>
      <slot name="label"></slot>
      <br>
      <button class="cursor-pointer">
        <slot name="default"></slot>
      </button>
    </label>
  </div>
</template>
<script>
export default {
  name: 'BaseButton'
}
</script>

<style scoped>

</style>
