<template>
  <view
   @touchstart="handleTouchstart"
    @touchend="handleTouchend"
  >
      <slot></slot>
  </view>
</template>

<script>
//1 slot 
//2 对外提供数据 滑动的方向
export default {
    data(){
        return{
            //按下的时间
            startTime:0,
            //按下的坐标
            startX:0,
            startY:0
        }
    },
    methods:{
    //用户按下屏幕
        handleTouchstart(event){
            
            this.startTime=Date.now();
            this.startX=event.changedTouches[0].clientX;
            this.startY=event.changedTouches[0].clientY;
        },
        handleTouchend(event){
            
            const endTime=Date.now();
            const endX=event.changedTouches[0].clientX;
            const endY=event.changedTouches[0].clientY;

            //判断按下的时长
            if(endTime-this.startTime>2000){
                return;
            }

            //滑动的方向 搞个变量
            let direction="";

            //先判断用户滑动的距离 是否合法 合法的话： 判断滑动的方向 注意 距离要加上绝对值
            //绝对值大于10像素的话
            if(Math.abs(endX-this.startX)>10 && Math.abs(endY-this.startY)<10){
                //判断滑动方向
                direction=endX-this.startX>0?"right":"left";
            }
            else{
                return;
            }

            //用户做了合法的滑动操作;
            //console.log(direction);
            //为了以后的维护 最好将单独的数据改为对象的模式,因为对象可以无限新增
            //这是向父组件传递信息
            this.$emit("swiperAction",{direction});
        }
    }
}
</script>

<style>

</style>