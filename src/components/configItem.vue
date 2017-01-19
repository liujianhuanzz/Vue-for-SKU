<template lang="html">
  <div class="configItem">
    <span>配置：</span>
    <span v-for="item in configType"
          :class="{disabled: item.state === 2, active: item.state === 1}"
          @click="configItemClick(item)">
      {{item.type}}
    </span>
  </div>
</template>

<script>
export default {
  name: 'configItem',
  props:['configType'],
  data(){
    return {

    }
  },
  methods: {
    configItemClick(item){
      this.$root.eventHub.$emit('config-change',item);
    }
  }
}
</script>

<style lang="css" scoped>
.configItem{
  width: 100%;
}
.configItem span:nth-child(n+2){
  display: inline-block;
  width: 210px;
  text-align: center;
  border: 2px solid #000;
  margin: 5px;
  padding: 3px 5px;
  cursor: pointer;
}
.configItem span.active{
  border-color: red;
  color: red;
}
.configItem span.disabled{
  border: 2px dashed #ccc;
  color: #ccc;
}
</style>
