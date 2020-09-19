<template>
  <view>
      <!-- 专辑背景 开始 -->
      <view class="album_bg">
          <image mode="widthFix" :src="album.cover" ></image>
          <view class="album_info">
              <view class="album_name">{{album.name}}</view>
              <view class="album_btn">关注专辑</view>
          </view>
      </view>
      <!-- 专辑背景 结束 -->

        <!-- 专辑作者 开始 -->
        <view class="album_author">
            <view class="album_author_info">
                <image mode="widthFix" :src="album.user.avatar"></image>
                <view class="author_name">{{album.user.name}}</view>
            </view>
            <view class="album_author_desc">
                <!-- 使用text使用一些/n之类的 -->
                <text>{{album.desc}}</text>
            </view>
        </view>
        <!-- 专辑作者 结束 -->

        <!-- 列表 开始 -->
        <view class="album_list">
            <view class="album_item"
            v-for="(item,index) in wallpaper"
            :key="item.id"
            >
            <go-detail :list="wallpaper" :index="index">
                <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)"></image>
            </go-detail>

            </view>
        </view>
        <!-- 列表 结束 -->
  </view>
</template>

<script>
import goDetail from "@/components/goDetail";

export default {
    components:{goDetail},
    data(){
        return{
            params:{
                limit:30,
                order:"new",
                skip:0,
                //当first的值为1 的时候 返回值是有 album对象 表示第一次请求数据
                //当first的值为0 的时候 返回值只有wallpaper数组 不是第一次请求数据
                first:1
            },
            id:-1,
            album:{},
            wallpaper:[],
            hasMore:true
        }
    },
    onLoad(options){
        //console.log(options); 获取id 然后赋值 接着执行方法 请求数据 打印数据
        this.id=options.id;
        //this.id="5d5f8e45e7bce75ae7fb8278";
        this.getList();
    },
    //页面触底 上拉加载下一页事件
    onReachBottom(){
        //console.log("触底了");
        if(this.hasMore){
            this.params.first=0;
            this.params.skip+=this.params.limit;
            this.getList();
        }
        else{
            uni.showToast({
                title:"没有更多数据了",
                icon:"none"
            })
        }
    },

    methods:{
        getList(){
            this.request({
                
                url:`http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
                //url:`https://service.picasso.adesk.com/v1/wallpaper/album/${this.id}/wallpaper`,
                data:this.params
            })
            .then(result=>{
                //console.log(result);
                //Object.keys会接收一个对象 如果里面存在属性 那么返回值就是个数组,数组是有长度的
                if(Object.keys(this.album).length===0){
                    this.album=result.res.album;
                }
                if(result.res.wallpaper.length===0){
                    this.hasMore=false;
                    uni.showToast({
                    title:"没有更多数据了",
                    icon:"none"
                    })
                    return;
                }
                //解构数组 拼接新数组
                this.wallpaper=[...this.wallpaper,...result.res.wallpaper];
            })
        }
    }
}
</script>

<style lang="scss" scoped>
//专辑页面开始
.album_bg {
    position: relative;
  image {

  }

  .album_info {
      position: absolute;
      width: 100%;
      left: 0;
      bottom: 0;
      display: flex;
      justify-content: space-between;
      height: 80rpx;
      align-items: center;
      color: #fff;
      //padding 上下为0 左右15
      padding: 0 15rpx;
    .album_name {
        font-size: 40rpx;
    }

    .album_btn {
        background-color: $color;
        width:152rpx;
        height: 60rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 10rpx;
    }
  }
}
//专辑背景结束

//开始
.album_author {
    padding: 10rpx;
  .album_author_info {
      padding: 10rpx 0;
      display: flex;
    image {
        width: 50rpx;
    }

    .author_name {
        color: #000;
        margin-left: 15rpx;
    }
  }

  .album_author_desc {

  }
}
//结束

//图片开始
.album_list {
    display: flex;
    //换行效果
    flex-wrap: wrap;
  .album_item {
      width: 33.33%;
      border:3rpx solid #fff;
    image {
        height: 160rpx;
    }
  }
}
//结束
</style>