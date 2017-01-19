<template lang="html">
  <div class="colorItem">
    <span>颜色：</span>
    <span v-for="item in colorType"
          :class="{disabled: item.state === 2, active: item.state === 1}"
          @click="colorItemClick(item)">
      {{item.type}}
    </span>
  </div>
</template>

<script>
export default {
  name: 'colorItem',
  props:['colorType'],
  data(){
    return {}
  },
  created(){
    
  },
  methods:{
    colorItemClick(item){
      this.$root.eventHub.$emit('color-change',item);
    }
  }
}
</script>

<style lang="css" scoped>
.colorItem{
  width: 100%;
}
.colorItem span:nth-child(n+2){
  display: inline-block;
  width: 70px;
  text-align: center;
  border: 2px solid #000;
  margin: 5px;
  padding: 3px 5px;
  cursor: pointer;
}
.colorItem span.active{
  border-color: red;
  color: red;
}
.colorItem span.disabled{
  border: 2px dashed #ccc;
  color: #ccc;
}
</style>
