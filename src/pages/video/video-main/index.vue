<template>
  <scroll-view
  scroll-y
  enable-flex
  class="video_main"
  @scrolltolower="handleScrolltolower"
  >
    <view class="video_item"
    v-for="item in videowp"
    :key="item.id"
    @click="handleGoVideo(item)"
    >
        <image mode="widthFix" :src="item.img"></image>
    </view>
  </scroll-view>
</template>

<script>
export default {
    //接收来自父组件的数据
    props:{
        urlobj:Object
    },
    data(){
        return{
            videowp:[],
            hasMore:true
        }
    },

    //watch属性是和vue相关的属性 他的作用是监控 当代码发生改变之后，他就会执行一次 发送新的请求即可
    watch:{
        urlobj(){
            //console.log("参数发生变化了")
            //console.log(this.urlobj);
            this.videowp=[];
            this.getList();
        }
    },
    //mounted 只执行 1 次，虽然数据变化了，可以用 watch 监听数据的变化 只会在页面的启动下 执行一次
    mounted(){
        //console.log(this.urlobj);
        this.getList();
    },

    methods:{
        getList(){
            this.request({
                //url是请求的链接
                url:this.urlobj.url,
                //data是请求的参数
                data:this.urlobj.params
            }).then(result=>{
                //console.log(result);
                if(result.res.videowp===0){
                    this.hasMore=false;
                     uni.showToast({
                    title:"没有更多数据了",icon:"none"
                    })
                    return;
                }
                this.videowp=[...this.videowp,...result.res.videowp];
            })
        },

        //分页事件 值都是父组件传过来的
        handleScrolltolower(){
            if(this.hasMore){
                this.urlobj.params.skip+=this.urlobj.params.limit;
                this.getList();
            }
            else{
                uni.showToast({
                    title:"没有更多数据了",icon:"none"
                })
            }
        },

        //图片点击事件 跳转视频
        handleGoVideo(item){
            //1 将数据存到全局共享的缓存中
            getApp().globalData.video=item;
            //2 页面跳转
            uni.navigateTo({
                url:"/pages/videoPlay/index"
            })
        }
    }

}
</script>

<style lang="scss" scoped>
.video_main{
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);
    .video_item{
        width: 33.33%;
        border:4rpx solid #fff;
    }
}
</style>