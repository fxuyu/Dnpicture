<template>
<view>
    <view class="category_tab">
        <view class="category_tab_title">
            <view class="title_inner">
            <uni-segmented-control 
            :current="current" 
            :values="items.map(v=>v.title)" 
            @clickItem="onClickItem" style-type="text" active-color="#d4237a"></uni-segmented-control>
            </view>
            <view class="iconfont icon-yixianshi-"></view>
        </view>

        <!-- 图片的显示开始 如果要让滚动标签拥有flex属性的话 需要添加enable-flex -->
        <scroll-view @scrolltolower="handleScrolltolower" enable-flex scroll-y class="category_tab_content">
           <view class="cate_item"
           v-for="item in vertical"
           :key="item.id"
           >
            <image :src="item.thumb" mode="widthFix"></image>
           </view>
        </scroll-view>
        <!-- 显示的结束 -->
    </view>
  </view>
</template>

<script>
import {uniSegmentedControl} from '@dcloudio/uni-ui'
export default {
    components:{
        uniSegmentedControl
    },

    data(){
        return{
            items: [
                {title:"最新",order:"new"},
                {title:"热门",order:"hot"},
            ],
            //这个是默认值 默认一进页面显示哪个
            current: 0,
            params:{
                limit:30,
                skip:0,
                order:"new"
            },
            id:0,
            //页面显示数据的数组
            vertical:[],
            //有没有下一页的数据 hasMore
            hasMore:true
        }
    },

    onLoad(options){
        this.id=options.id;
        this.getList();
    },

    methods: {
        //点击切换效果
        onClickItem(e) {
            /**
             * 1 根据被点击的标题 切换 标题 
             * 2 根据被点击的标题来切换的order
             * 3 重新发送请求
             */
            if (this.current !== e.currentIndex) {
                this.current = e.currentIndex;
            }
            else{
                //这是点击相同的标题的意思
                return;
            }
            this.params.order=this.items[e.currentIndex].order;
            //点击切换效果后 需要把skip和vertical清空 
            this.params.skip=0;
            this.vertical=[];
            this.getList();
        },
        //发送请求
        getList(){
            this.request({
                //url:`http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
                url:`https://service.picasso.adesk.com/v1/vertical/category/${this.id}/vertical`,
                data:this.params
            }).then(result=>{
                //console.log(result);
                if(result.res.vertical.length===0){
                    this.hasMore=false;
                    uni.showToast({
                        title:"没有更多数据了",
                        icon:"none"
                    })
                    return;
                }
                //下面拿到数组之后 应该是叠加 而不是再赋值。
                this.vertical=[...this.vertical,...result.res.vertical];
            })
        },

        //加载下一页的数据
        handleScrolltolower(){
            if(this.hasMore){
                this.params.skip+=this.params.limit;
                this.getList();
            }
            else{
                uni.showToast({
                    title:"没有更多的数据了",
                    icon:"none"
                })
            }
        }
    }
}
</script>

<style lang="scss">
.category_tab{
    .category_tab_title{
        position: relative;
        .title_inner{
            width: 60%;
            margin: 0 auto;
        }
        .icon-yixianshi-{
            position: absolute;
            top:50%;
            transform: translateY(-50%);
            right: 5%;
        }
    }
    .category_tab_content{
        display: flex;
        flex-wrap: wrap;
        height: calc(100vh - 36px);
        .cate_item{
            width: 33%;
            border:4rpx solid #fff;
            image{

            }
        }
    }
}
</style>