<template>
<div class="bar">
  <h5>{{name}}</h5>
  <div class="bar-container">
    <div class="value" :style="styles" :class="[isMax?'maxed':'']">{{value}}</div>
  </div>
</div>
</template>

<script>
export default {
  name: `Bar`,
  props:{
    value: Number,
    maxValue: Number,
    name: String
  },
  computed: {
    styles(){
      return{
        "width": `${(this.value / this.maxValue ) * 100}%`,
      };
    },
    isMax(){
      return this.value === this.maxValue && this.maxValue != 0;
    }
  }
}
</script>

<style scoped>
.bar{
  width: 100%;
  display: grid;
  grid-template-columns: 1fr 9fr;
}

.value{
  left: 0;
  margin: 0;
  text-align: right;
  padding: 5px;
  border-radius: 5px;
}
.value{
  background: #818384;
}
.maxed{
  background: #538d4e;
}



.bar-container{
  width: 100%;
  height: 20px;
}

</style>