<template>
  <div>
    <label class="cursor-pointer">
      {{ label }}
      <br>
      <input :placeholder="placeholder" :value="value" min="0" type="number" @input="updateValue">
    </label>
  </div>
</template>
<script>
export default {
  name: 'BaseInput',
  props: {
    label: {
      type: String,
      default: ''
    },
    placeholder: {
      type: String,
      default: ''
    },
    value: {
      type: Number,
      default: 0
    }
  },

  methods: {
    updateValue (event) {
      this.$emit('input', Number(event.target.value))
    }
  }
}
</script>

<style scoped>

</style>
