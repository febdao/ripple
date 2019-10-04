<template>
  <select @change="$emit('search-sort-change', selected)" v-model="selected">
    <option v-for="(option, index) in options" :key="`${index}-option`" :value="option.value">{{option.label}}</option>
  </select>
</template>

<script>
export default {
  props: {
    options: {
      type: Array
    }
  },
  data () {
    return {
      selected: this.options[0]
    }
  }
}
</script>
