<template>
  <div>
    <ul>
      <li
          v-for="(event, index) in events"
          :key="`${event}-${index}`"
          :class="event.type === MESSAGE_TYPES.success ? MESSAGE_TYPES.success : MESSAGE_TYPES.error"
      >
        {{ event.date }} :: {{ event.message }} :: {{ event.data }}
      </li>
    </ul>
  </div>
</template>
<script>
import {MESSAGE_TYPES} from '@/constants'

export default {
  name: 'TheInformation',
  computed: {
    MESSAGE_TYPES () {
      return MESSAGE_TYPES
    }
  },
  props: {
    events: {
      type: Array,
      default: () => []
    }
  }
}
</script>

<style scoped>
.success {
  color: green;
}

.error {
  color: red;
}
</style>
