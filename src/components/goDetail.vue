<template>
  <view @click="handleClick">
      <slot></slot>
  </view>
</template>

<script>
export default {
    props:{
        //这是个对象
        list:Array,
        //这是个索引
        index:Number
    },
    methods:{
        handleClick(){
            //1将数据缓存下来 存到globalData
            getApp().globalData.imgList=this.list;
            getApp().globalData.imgIndex=this.index;
            //2再实现点击跳转页面效果 通过js的方式跳转
            uni.navigateTo({
                url:"/pages/imgDetail/index"
            })
        }
    },
    //mounted 是当组件挂载完毕后 使用
    mounted(){
        //console.log(this.list);
        //console.log(this.index);
    }
}
</script>

<style>

</style>