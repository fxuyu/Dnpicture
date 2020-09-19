<template>
  <view>
    <!-- 用户信息开始 -->
    <view class="user_info">
      <view class="user_icon">
        <image :src="imgDetail.user.avatar" mode="widthFix"></image>
      </view>
      <view class="user_desc">
        <view class="user_name">{{imgDetail.user.name}}</view>
        <view class="user_time">{{imgDetail.cnTime}}</view>
      </view>
    </view>
    <!-- 用户信息结束 -->

    <!-- 高清大图开始  会有undefind报错是因为这个视图层先渲染 而后再执行script层面 渲染的时候 没有数据 所以报错 -->
    <view class="high_image">
      <swiper-action @swiperAction="handleSwiperAction">
        <image mode="widthFix" :src="imgDetail.thumb" ></image>
      </swiper-action>
    </view>
    <!-- 高清大图 结束 -->

    <!-- 点赞 开始 -->
    <view class="user_rank">
      <view class="rank">
        <text class="iconfont icondianzan">{{imgDetail.rank}}</text>
      </view>
      <view class="user_collect">
        <text class="iconfont iconshoucang">收藏</text>
      </view>
    </view>
    <!-- 点赞 结束 -->

    <!-- 专辑 开始-->
    <view class="album_wrap" v-if="album.length">
      <!-- 标题 -->
      <view class="album_title">相关</view>
      <!-- 内容 -->
      <view class="album_list">
        <view class="album_item"
        v-for="item in album"
        :key="item.id">
          <view class="album_cover">
            <image :src="item.cover" mode="aspectFill"></image>
          </view>
          <!-- 右边是专辑详细信息 -->
          <view class="album_info">
            <view class="album_info_text">专辑</view>
            <view class="album_name">{{item.name}}</view>
            <text class="iconfont iconiconfontjiantou4">NEXT</text>
          </view>
        </view>
      </view>
    </view>
    <!-- 专辑 结束 -->

    <!-- 最热评论 开始 comment hot -->
    <view class="comment hot" v-if="hot.length">
      <view class="comment_title">
        <text class="iconfont iconhot1">火</text>
        <text class="comment_text">最热评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item"
        v-for="item in hot"
        :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户的头像 -->
            <view class="user_icon">
              <image mode="widthFix" :src="item.user.avatar"></image>
            </view>
            <!-- 用户的名称 -->
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
              v-for="item2 in item.user.title"
              :key="item2.icon"
              :src="item2.icon"></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">赞{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!--最热评论 结束 -->

    <!-- 最新评论 开始 comment new -->
    <view class="comment new" v-if="comment.length">
      <view class="comment_title">
        <text class="iconfont iconpinglun">新</text>
        <text class="comment_text">最热评论</text>
      </view>
      <view class="comment_list">
        <view class="comment_item"
        v-for="item in comment"
        :key="item.id">
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户的头像 -->
            <view class="user_icon">
              <image mode="widthFix" :src="item.user.avatar"></image>
            </view>
            <!-- 用户的名称 -->
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
              v-for="item2 in item.user.title"
              :key="item2.icon"
              :src="item2.icon"></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">赞{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!--最新评论 结束 -->

    <!-- 下载图片 开始 -->
      <view class="download">
        <view class="download_btn" @click="handleDownload">下载图片</view>
      </view>
    <!-- 下载图片 结束 -->
  </view>
</template>

<script>
import moment from "moment";
import swiperAction from "@/components/swiperAction";
//设置时间格式为中文格式
moment.locale("zh-cn");
export default {
  components:{
    swiperAction
  },
    data(){
      return{
        //图片信息对象 包含着用户头像
        imgDetail:{},
        //专辑数据 数组格式
        album:[],
        //最热评论
        hot:[],
        //最新评论
        comment:[],
        //图片的索引
        imgIndex:0
      }
    },
    onLoad(){
        console.log(getApp().globalData)
        const {imgIndex}=getApp().globalData;

        this.imgIndex=imgIndex;
        this.getData();
    },

    methods:{
      //给当前页面赋值
      getData(){
        const {imgList}=getApp().globalData;
        this.imgDetail=imgList[this.imgIndex];
        //this.imgDetail.newThumb=this.imgDetail.thumb + this.imgDetail.rule.replace('$<Height>',360);

        //时间属性
        this.imgDetail.cnTime=moment(this.imgDetail.atime*1000).fromNow();

        //获取图片详情的id
        //this.imgDetail.id
        this.getComments(this.imgDetail.id);
      },

      getComments(id){
        this.request({
          url:`https://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment`
        })
        .then(result=>{
          console.log(result);
          this.album=result.res.album;
          //给hot数组中的对象中添加一个时间属性 转为xxx月前 年前的 格式  v就是每个hot数组里面的对象 给他动态添加属性
          result.res.hot.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow());
          result.res.comment.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow());
          this.hot=result.res.hot;
          this.comment=result.res.comment;
        })
      },
      //滑动事件
      handleSwiperAction(e){
        /**
         * 我们可以在e上面获取用户左滑右滑的事件
         * 1 用户 左滑 imgindex++
         * 2 用户 右滑 imgindex--
         * 3 判断 数组是否越界的问题！
         * 4 当我们屏幕进行左滑的时候 e.direction==="left"&&this.imgIndex<imgList.length-1
         * 5 当我们屏幕进行右滑的时候 e.direction==="right"&&this.imgIndex>0
         */
        const {imgList}=getApp().globalData;
        if(e.direction==="left"&&this.imgIndex<imgList.length-1){
          //可以进行左滑的的操作 加载下一页
          this.imgIndex++;
          this.getData();
        }
        else if(e.direction==="right"&&this.imgIndex>0){
          //右滑 加载上一页
          this.imgIndex--;
          this.getData();
        }
        else{
          uni.showToast({
            title:"没有数据了",
            icon:"none"
          })
        }
        console.log(e);
      },

      //点击下载图片 使用 async await (异步回调代码的最终操作)  promise 
      async handleDownload(){
        //uni.downloadFile
        //uni.saveImageToPhotosAlbum
       
        await uni.showLoading({
          title:"下载中"
        })

         //第一步 将远程文件下载到小程序的内存中 tempFilePath
        const result1=await uni.downloadFile({url:this.imgDetail.img});
        const {tempFilePath}=result1[1];
        
        //2 将小程序内存中的临时文件下载到本地上
        const result2=uni.saveImageToPhotosAlbum({
          filePath:tempFilePath
        });
        console.log(result2);
        //3 提示用户下载成功
        uni.hideLoading();
        await uni.showToast({
          title:"下载成功",
        })
      }
    }
}
</script>

<style lang="scss" scoped>
.user_info {
  display: flex;
  padding: 20rpx;
  .user_icon {
    padding: 0 20rpx;

    image {
      width: 88rpx;
      height: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
      color: #000;
      font-weight: 600;
    }

    .user_time {
      color: #ccc;
      font-size: 24rpx;
      padding: 10rpx 0;
    }
  }
}

// 收藏功能
.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 5rpx solid #eee;
  .rank {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    border-right: 2rpx solid #eee;
    .iconfont {

    }
  }

  .user_collect {
    flex:1;
    display: flex;
    justify-content: center;
    align-items: center;
    .iconfont {

    }
  }
}

.high_image{
  image{
    border-bottom: 1rpx solid #eee;
  }
}

//专辑标签结构
.album_wrap {
  padding: 20rpx;
  .album_title {
    padding: 10rpx 0;
  }

  .album_list {
    .album_item {
      display: flex;
      padding: 10rpx 0;
      border-bottom: 10rpx solid #eee;
      .album_cover {
        flex:1;
        image {
          width: 180rpx;
          height: 180rpx;
        }
      }

      

      .album_info {
        flex:3;
        padding-left: 20rpx;
        position: relative;
        .album_info_text {
          width: 100rpx;
          height: 50rpx;
          background-color: $color;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color:#888;
        }

        .iconfont{
          font-size: 40rpx;
          position: absolute;
          top:50%;
          //这个叫做CSS3中的 变换 或者叫 转换 技术
          transform: translateY(-50%);
          right: 10%;
          color: #000;
        }
      }
    }
  }
}

//最热评论
.comment {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: red;
      font-size: 40rpx;
    }

    .comment_text {
      //字体加粗
      font-weight: 600;
      font-size: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }
//这一共有两层结构 
.comment_list {
  .comment_item {
    border-bottom: 15rpx solid #eee;
    //用户信息
    .comment_user {
      display: flex;
      padding: 20rpx 0;
      .user_icon {
        width: 15%;
        display: flex;
        justify-content: center;
        align-items: center;
        image {
          width: 90%;
        }
      }

      .user_name {
        flex:1;
        .user_nickname {
          color: #777;
        }

        .user_time {
          color:#ccc;
          font-size: 24rpx;
          padding: 5rpx;
        }
      }

      .user_badge {
        image {
          width: 40rpx;
          height: 40rpx;
          //行内块，就会让他们一行显示
          display: inline-block;
        }
      }
    }
    //评论数据
    .comment_desc {
      display: flex;
      padding:30rpx 0;
      .comment_content {
        flex:1;
        padding-left: 15%;
        color: #000;
      }

      .comment_like {
        text-align: right;
        text.iconfont.icondianzan {

        }
      }
    }
  }
}
}

//最新评论
.new{
  .iconpinglun{
    //设定优先级为最高 !important
    color: aqua!important;
  }
}

//下载按钮
.download{
  height: 120rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  .download_btn{
    width: 90%;
    height: 75%;
    background-color: $color;
    color: #fff;
    font-size: 50rpx;
    font-weight: 600;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>