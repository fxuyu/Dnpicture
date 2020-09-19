<template>
  <view class="video_play">
      <!-- 背景图片 开始 -->
      <image :src="videoObj.img"></image>
      <!-- 背景图片的结束 -->
      <!-- 工具栏开始 绑定一个handleMuted的事件实现取反  微信小程序自带转发按钮 所以添加就<button open-type="share" ></button>行了-->
      <view class="video_tool">
          <view @click="handleMuted" :class="['iconfont',muted?'iconjingyin':'iconshengyin']">声</view>
          <view class="iconfont iconzhuanfa">享
              <button open-type="share" ></button>
          </view>
      </view>
      <!-- 工具栏 结束 -->

        <!-- 视频 开始 muted可以控制声音 objectFit可以拉伸视频 -->
        <view class="video_wrap">
            <video :muted="muted" :src="videoObj.video" objectFit="fill"></video>
        </view>
        <!-- 视频 结束 -->
        <!-- 下载开始 -->
        <view class="download">
            <view class="download_btn" @click="handleDownload">下载视频</view>
        </view>
        <!-- 下载 结束 -->
  </view>
</template>

<script>
export default {
    data(){
        return{
            videoObj:{},
            //是否静音
            muted:false
        }
    },
    onLoad(){
        //console.log(getApp().globalData);
        this.videoObj=getApp().globalData.video;
    },
    methods:{
        //开关声音
        handleMuted(){
            this.muted=!this.muted;
        },
        //下载视频
        async handleDownload(){
            await uni.showLoading({title:"下载中"});

            //1 将远程文件（视频）下载到小程序内存中 
            //这里拿的是整段代码的返回值 临时地址 [0]第0的元素是状态码 [1]第一的的元素才是返回值对象
            const { tempFilePath } = (
                await uni.downloadFile({url: this.videoObj.video })
                )[1];
            //2 将内存中的文件 下载到本地上
            await uni.saveVideoToPhotosAlbum({
                filePath:tempFilePath
            });
            uni.hideLoading();
            await uni.showToast({title:"下载成功"})
        }
    }
}
</script>

<style lang="scss" scoped>
.video_play {
  position: relative;
  image {
      position: absolute;
      width: 100vw;
      height: 100vh;
      //z-index -1 表示这个页面不会挡住前面的这些标签了
      z-index: -1;
      //css3的滤镜属性 高斯模糊
      filter: blur(20px);
  }

  .video_tool {
      height: 80rpx;
      display: flex;
      //让元素右对齐 flex-start 是左对齐
      justify-content: flex-end;
    .iconfont{
        width: 80rpx;
        color: #fff;
        font-size: 50rpx;
        border-radius: 40rpx;
        //加点透明度
        background-color: rgba(0, 0, 0, 0.2);
        display: flex;
        justify-content: center;
        align-items: center;
        margin-right: 20rpx;
    }
    .iconzhuanfa{
        position: relative;
        button{
            position: absolute;
            width: 100%;
            height: 100%;
            opacity: 0;
        }
    }
  }

  .video_wrap {
      display: flex;
      justify-content: center;
    video {
        width: 360rpx;
        height: 600rpx;
    }
  }

  .download {
      display: flex;
      justify-content: center;
      margin-top: 30rpx;
    .download_btn {
        width: 360rpx;
        height: 80rpx;
        border-radius: 40rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        color:#fff;
        border:3rpx solid #fff;
        background-color: rgba(0,0,0,0.6);
    }
  }
}
</style>