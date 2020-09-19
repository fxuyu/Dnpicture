<template>
  <view
  @touchstart="handleTouchstart"
  @touchend="handleTouchend"
  >学习触屏事件</view>
</template>

<script>
/**
 * 1 给容器绑定两个触屏事件 touchstart 和 touchend
 * 2 用户按下屏幕事件后 
 *  1 记录用户按下屏幕的事件 Date.now() 返回的是 时间戳 返回 1970 -1-1 到现在的毫秒数
 *  2 记录用户按下屏幕的坐标 X和Y
 * 3 用户离开屏幕的事件
 *  1 记录用户离开屏幕的时间 Date.now()
 *  2 记录用户离开屏幕的坐标 x 和 y
 *  3 根据两个时间 运算 判断 用户 按下屏幕时长是否合法
 *  4 根据 两对坐标（按下和离开的XY坐标） 判断距离是否合法 合法 再判断 滑动的方向 
 */
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
            console.log("X坐标"+event.changedTouches[0].clientX);
            console.log("y坐标"+event.changedTouches[0].clientY);
            this.startTime=Date.now();
            this.startX=event.changedTouches[0].clientX;
            this.startY=event.changedTouches[0].clientY;
        },
        handleTouchend(event){
            console.log("X坐标"+event.changedTouches[0].clientX);
            console.log("y坐标"+event.changedTouches[0].clientY);
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
            if(Math.abs(endX-this.startX)>10){
                //判断滑动方向
                direction=endX-this.startX>0?"right":"left";
            }
            else{
                return;
            }

            //用户做了合法的滑动操作;
            console.log(direction);
        }
    }
}
</script>

<style>
view{
    width: 100%;
    height: 500rpx;
    background-color: aqua;
}
</style>