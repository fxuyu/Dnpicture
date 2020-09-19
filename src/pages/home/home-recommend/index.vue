<template>
  <scroll-view @scrolltolower="handleToLower" class="recommends_view" scroll-y v-if="recommends.length>0">
    <!-- 推荐开始 -->
    <view class="recommend_wrap">
      <navigator class="recommend_item"
      v-for="item in recommends"
      :key="item.id"
      :url="`/pages/album/index?id=${item.target}`"
      >
        <image mode="widthFix" :src="item.thumb"></image>
      </navigator>
    </view>
    <!-- 推荐结束 -->

    <!-- 月份相关 开始 -->
    <view class="months_wrap">
      <view class="months_title">
        <view class="months_title_info">
          <view class="months_info">
            <text>{{monthes.DD}} /</text>
            {{monthes.MM}}月
          </view>
          <view class="months_text">{{monthes.title}}</view>
        </view>
        <view class="months_title_more">更多</view>
      </view>
      <!-- 图片 -->
      <view class="months_content">
        <view class="months_item"
        v-for="(item,index) in monthes.items"
        :key="item.id">
            <go-detail :list="monthes.items" :index="index">
              <image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>',360)"> </image>
            </go-detail>
            
        </view>
      </view>
    </view>
    <!-- 月份相关 结束 -->

    <!-- 热门开始 -->
      <view class="hots_wrap">
        <view class="hots_title">
          <text>热门</text>
        </view>
        <view class="hots_content">
          <view class="hot_item" v-for="(item,index) in hots" :key="item.id">
            <go-detail :list="hots" :index="index">
              <image mode="widthFix" :src="item.thumb"></image>
            </go-detail>
            
          </view>
        </view>
      </view>
    <!-- 热门结束 -->
  </scroll-view>
</template>

<script>
import moment from "moment";
import goDetail from "@/components/goDetail";
export default {
  components:{
    goDetail
  },

  //data是一个函数 返回一个对象
  data(){
    return{
      //推荐列表
      recommends:[],
      //月份模块
      monthes:{},
      //热门
      hots:[],
      //请求的参数 在触底需要触发再加载的事件时 需要跳过几条数据这里 需要形参
      params:{
        //要获取几条数据
        limit:21,
        //关键字
        order:"hot",
        //要跳过几条数据
        skip:0
      },
      //是否还有下一页数据
      hasMore:true
    }
  },
  mounted(){
    uni.setNavigationBarTitle({title:"推荐"});
    this.getList();
  },
  //methods主要是定义方法的
  methods:{
    //获取接口的数据
    getList(){
      this.request({
      url:"http://157.122.54.189:9088/image/v3/homepage/vertical",
      data:this.params
    }).then(result=>{
      console.log(result);
      //判断还有没有下一页的数据
      if(result.res.vertical.length===0){
        this.hasMore=false;
        uni.showToast({
                title:"没有更多数据了",
                icon:"none"
            })
        return;
      }

      if(this.recommends.length===0){
        //console.log(result);
        //就说明这是第一次发送请求
        //头部的推荐模块
      this.recommends=result.res.homepage[1].items;
      //月份的模块
      this.monthes=result.res.homepage[2];
      //将时间戳 改成 18号/月 moent.js 可以修改成我们看得懂的时间 format格式化
      this.monthes.MM=moment(this.monthes.stime).format("MM");
      this.monthes.DD=moment(this.monthes.stime).format("DD");
      }
      
      //获取热门数据的列表
      //使用数组拼接的方式 用es6的方法
      this.hots=[...this.hots,...result.res.vertical];
    })
    },

    //滚动条触底事件
    handleToLower(){
      /**
       * 1 当滚动条触底的时候 修改请求参数->skip skip+=limit
       * 2 参数改变完毕后 需要重新发送请求 getList
       * 3 请求回来成功后 hots 数据的叠加
       */
      if(this.hasMore){
        this.params.skip += this.params.limit;
        this.getList();
      }
      else{
        //弹窗提示用户
        uni.showToast({
          title:"没有了",
          icon:"none"
        })
      }
      
      
    }
  }
}
</script>

<style lang="scss" scoped>

.recommends_view{
  //他的高度必须等于 屏幕的高度-头部的标题高度
  //需要借助其他方式来计算 calc(100vh)等于整个屏幕的高度了 36px是头部顶部的高度 减号的两边要留空 
  height: calc(100vh - 36px);
}

// scoped的属性与vue相关
//推荐开始
  .recommend_wrap{
    display: flex;
    flex-wrap:wrap;
    .recommend_item{
      width: 50%;
      border:3rpx solid #fff;
    }
  }

  //月份的开始
.months_wrap {
  .months_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    .months_title_info {
      display: flex;
      color:$color;
      font-size:30rpx;
      font-weight: 600;
      .months_info {
        text {
          font-size:36rpx;
        }
      }

      .months_text {
        font-size:30rpx;
        color:#666;
        margin-left: 30rpx;
      }
    }

    .months_title_more {
      font-size: 24rpx;
      color:$color;
    }
  }

  .months_content {
    display: flex;
    //自动换行
    flex-wrap: wrap;
    .months_item{
      width: 33.33%;
      border:3rpx solid #fff;
    }
  }
}

//热门开始
.hots_wrap {
  .hots_title {
    padding: 20rpx;
    text {
      border-left: 20rpx solid $color;
      padding-left: 20rpx;
      font-size: 30rpx;
      font-weight: 600;
    }
  }

  .hots_content {
    display: flex;
    flex-wrap: wrap;
    .hot_item {
      width: 33.33%;
      border:4rpx solid #fff;
      image {

      }
    }
  }
}

</style>