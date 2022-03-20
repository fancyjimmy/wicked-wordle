<template>
<div class="bar">
  <div class="value"></div>
</div>
</template>

<script>
export default {
  name: `Bar`,
  props:{
    value: Number,
    maxValue: Number,
    name: String
  }
}
</script>

<style scoped>

</style>