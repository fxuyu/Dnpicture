<template>
  <scroll-view class="album_scroll_view" scroll-y @scrolltolower="handleToLower">
    <!-- swiper 默认高度是150px image 默认的宽度为320px 默认高度是240px
      要做到轮播图的屏幕自适应 需要
      计算图片的宽度和高度的比例 把图片的比例也写到swiper 标签样式
      swiper-item 默认宽高是100%
     -->
    <!-- 轮播图开始 swiper 三个属性 1 自动轮播 2指示器（小圆点） 3衔接轮播 -->
    <view class="album_swiper">
      <swiper autoplay indicator-dots circular>
        <swiper-item 
        v-for="item in banner" :key="item.id"
        >
          <image  :src="item.thumb"></image>
        </swiper-item>
      </swiper>
    </view>
    <!-- 轮播图结束 -->

    <!-- 列表开始 -->
    <view class="album_list">
      <navigator class="album_item"
      v-for="item in album"
      :key="item.id"
      :url="`/pages/album/index?id=${item.id}`"
      >
        <view class="album_img">
          <image mode="aspectFill" :src="item.cover"></image>
        </view>
        <view class="album_info">
          <view class="album_name">{{item.name}}</view>
          <view class="album_desc">{{item.desc}}</view>
          <view class="album_btn">
            <view class="album_attention">+ 关注</view>
          </view>
        </view>
      </navigator>
    </view>
    <!-- 列表结束 -->
  </scroll-view>

</template>

<script>
export default {
  data(){
    return{
      //轮播图数组
      banner:[],
      //专辑
      album:[],
      //是否还有分页数据
      hasMore:true,
      params:{
        //要获取几条数据
        limit:21,
        //关键字
        order:"new",
        //要跳过几条数据
        skip:0
      },
    }
  },
  mounted(){
    //mounted 组件加载完毕就会触发
    //修改页面的标题
    uni.setNavigationBarTitle({title:"专辑"});
    this.getList()
  },
  methods:{
    //获取接口的数据
    getList(){
      this.request({
        url:"http://157.122.54.189:9088/image/v1/wallpaper/album",
        data:this.params
      }).then(result=>{
          if(this.banner.length===0){
            //console.log(result);
            this.banner=result.res.banner;
          }
          if(result.res.album.length===0){
            this.hasMore=false;
            uni.showToast({
                title:"没有更多数据了",
                icon:"none"
            })
            return;
          }
        this.album=[...this.album,...result.res.album];
      })
    },
    //上拉加载-执行分页
    handleToLower(){
      if(this.hasMore){
        this.params.skip+=this.params.limit;
        this.getList();
      }
      else{
        uni.showToast({
          title:"没有数据了",
          icon:"none"
        })
      }
    }
  }

}
</script>

<style lang="scss" scoped>
.album_scroll_view{
  height: calc(100vh - 36px);
}

.album_swiper{
  swiper{
    //为了做到轮播图屏幕自适应 小程序基础750rpx  然后用这个基础去除以 图片的 高度除以宽度
    height:calc(750rpx / 2.3);
    image{
      height: 100%;
    }
  }
}

//列表开始
.album_list {
  padding: 10rpx;
  .album_item {
    padding: 10rpx 0;
    display: flex;
    border-bottom: 1px solid #ccc;
    .album_img {
      flex:1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex:2;
      padding: 0 5rpx;
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rpx 0;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;
        //给文字加上不要换行 一行显示 多余的显示...
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;
        .album_attention {
          color: $color;
          border:1rpx solid $color;
          padding: 10rpx;
        } 
      }
    }
  }
}
</style>