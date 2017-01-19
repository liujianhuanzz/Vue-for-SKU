<template lang="html">
  <div class="skuModule">
    <h2>Vue中SKU组合查询DEMO</h2>
    <color-item :colorType="this.skuType['color']"></color-item>
    <size-item :sizeType="this.skuType['size']"></size-item>
    <config-item :configType="this.skuType['config']"></config-item>
  </div>
</template>

<script>
import Vue from 'vue'
import colorItem from './colorItem'
import sizeItem from './sizeItem'
import configItem from './configItem'

export default {
  name: 'skuModule',
  components: {
    colorItem,
    sizeItem,
    configItem
  },
  data(){
    return {
      //此数据为后台传回来的某商品的sku数据，一般在created时向后台发起请求获取，此处模拟
      skuList: [
        {'color':'银色', 'size': '13.3英寸','config':'Core i5/8G内存/256G闪存'},
        {'color':'银色', 'size': '15.4英寸','config':'512闪存/Multi-Touch Bar'},
        {'color':'深空灰色', 'size': '13.3英寸','config':'256闪存/Multi-Touch Bar'},
        {'color':'银色', 'size': '13.3英寸','config':'512闪存/Multi-Touch Bar'},
        {'color':'深空灰色', 'size': '15.4英寸','config':'512闪存/Multi-Touch Bar'}
      ],
      //存储类别
      skuType:{},
      typeIsSelected:{},
      //存储每次的候选结果集
      skuResultList: []
    }
  },
  created(){
    //向后台发起请求获取sku数据,目前假设数据已经获取到，在这里进行分组
    this.typeInit();
    //类型加一个标识状态，0为未选，1为选中，2为不可
    this.addStateToType();
    //监听color-change事件
    this.$root.eventHub.$on('color-change', (target) => {
      this.colorChangeHandler(target);
    })

    //监听size-change事件
    this.$root.eventHub.$on('size-change', (target) => {
      this.sizeChangeHandler(target);
    })

    //监听config-change事件
    this.$root.eventHub.$on('config-change', (target) => {
      this.configChangeHandler(target);
    })
  },
  methods:{
    typeInit(){
      this.skuList.forEach((item) => {
        Object.keys(item).forEach((sitem) => {
          //skuType
          if(this.skuType[sitem] && this.skuType[sitem].indexOf(item[sitem]) === -1){
            this.skuType[sitem].push(item[sitem]);
          }else if(!this.skuType[sitem]){
            this.skuType[sitem] = [item[sitem]];
          }
          //typeIsSelected
          if(!this.typeIsSelected[sitem]){
            this.typeIsSelected[sitem] = 0;
          }
        })
      })
    },
    addStateToType(){
      for(let key in this.skuType){
        this.skuType[key].forEach((item, index) => {
          this.skuType[key][index] = {'type': item, 'state': 0};
        })
      }

      this.skuType = Object.assign({}, this.skuType);
    },
    colorChangeHandler(msg){
      this.attrChangeHandler('color', msg);
    },
    sizeChangeHandler(msg){
      this.attrChangeHandler('size', msg);
    },
    configChangeHandler(msg){
      this.attrChangeHandler('config', msg);
    },
    attrChangeHandler(attr, attrInfo){
      let attrState = attrInfo.state;//用来保存点击触发时候的状态
      //更改当前属性
      this.skuType[attr].forEach((item, index) => {
        if(item.type === attrInfo.type){
          item.state = 1;
        }else{
          item.state = 2;
        }
      })
      //更改其他属性, 首先统计有几个属性已被选择
      let count = 0;
      Object.keys(this.typeIsSelected).forEach((item) => {
        count += this.typeIsSelected[item];
      })
      //如果本次选择的是disabled，则必须重新从原始数据集进行筛选，
      if(attrState === 2){
        this.resetTypeSelectedState();
        this.selectRightData(this.skuList, attr, attrInfo);
      }
      //否则进入下一个逻辑
      else{
        //如果count = 0, 则是第一次选择，应该从原始数据集中进行筛选
        if(count === 0){
          this.resetTypeSelectedState();
          this.selectRightData(this.skuList, attr, attrInfo);
        }
        //如果count != 0，则是已有属性被选择，则需要判断这次是新选还是更改
        //新选则从上次的结果集进行筛选，更改则仍然从原始数据集进行筛选
        else{//新选
          this.selectRightData(this.skuResultList, attr, attrInfo);
        }
      }
    },
    //处理函数
    selectRightData(dataList, attr, attrInfo){

      this.typeIsSelected[attr] = 1;
      this.skuResultList = [];

      dataList.forEach((item) => {
        if(item[attr] === attrInfo['type']){
          this.skuResultList.push(item);
        }
      })

      //根据结果数据集进行对各个属性重新赋值渲染
      let tempSkuType = {};
      this.skuResultList.forEach((item) => {
        Object.keys(item).forEach((sitem) => {
          //skuType
          if(tempSkuType[sitem] && tempSkuType[sitem].indexOf(item[sitem]) === -1){
            tempSkuType[sitem].push(item[sitem]);
          }else if(!tempSkuType[sitem]){
            tempSkuType[sitem] = [item[sitem]];
          }
        })
      })

      for(let key in tempSkuType){
        if(this.typeIsSelected[key] === 0){
          for(let i=0; i<this.skuType[key].length; i++){
            let currEle = this.skuType[key][i];
            if(tempSkuType[key].indexOf(currEle['type']) > -1){
              currEle['state'] = 0;
            }else{
              currEle['state'] = 2;
            }
          }
        }
      }
    },
    resetTypeSelectedState(){
      for(let key in this.typeIsSelected){
        this.typeIsSelected[key] = 0;
      }
    }
  }
}
</script>

<style lang="css">
.skuModule{
  width: 800px;
  height: 700px;
  margin: 0 auto;
  text-align: left;
}
</style>
