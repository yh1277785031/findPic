<template>
  <scroll-view
    class="homePage"
    v-if="monthList.MM"
    scroll-y
    @scrolltolower="handleLower"
  >
    <!--  -->
    <view class="recommend">
      <navigator
        v-for="item in recommendList"
        :key="item.id"
        class="recommendItem"
        :url="`/pages/album/album?id=${item.target}`"
      >
        <img
          mode="widthFix"
          :src="item.thumb"
        >
      </navigator>
    </view>
    <!--  -->
    <view class="month_wrap">
      <view class="title">
        <view class="title_left">
          <view class="title_left_date">
            <text class="title_left_date_month">{{monthList.DD}} / </text> {{monthList.MM}} 月
          </view>
          <view class="title_left_info">{{monthList.title}}</view>
        </view>
        <view class="title_right">
          更多 >
        </view>
      </view>
      <view class="monthContent">
        <view
          v-for="(item,index) in monthList.items"
          :key="item.id"
          class="monthContentItem"
        >
          <ToDetail
            :list="monthList.items"
            :index="index"
          >
            <img
              :src="item.thumb + item.rule.replace('$<Height>',360)"
              mode="aspectFill"
            >
          </ToDetail>
        </view>
      </view>
    </view>
    <!-- 热门 -->
    <view class="hot">
      <view class="hot_title">
        <text>热门</text>
      </view>
      <view class="hot_content">
        <view
          v-for="(item,index) in hotList"
          :key="item.id"
          class="hot_contentItem"
          mode="widthFix"
        >
          <ToDetail
            :list="hotList"
            :index="index"
          >
            <img :src="item.thumb">
          </ToDetail>
        </view>
      </view>
    </view>
  </scroll-view>
</template>

<script>
import moment from 'moment'
import { ToDetail } from "../../components/ToDetail";
export default {
  data() {
    return {
      //   头部推荐列表
      recommendList: [],
      //   中间月份列表
      monthList: {},
      //   热门列表
      hotList: [],
      // 参数列表
      params: {
        limit: 30,
        order: "hot",
        skip: 0
      },
      hasMore: true
    }
  },
  components: {
    ToDetail,
  },
  mounted() {
    uni.setNavigationBarTitle({
      title: '推荐'
    })
    this.getList()
  },
  methods: {
    // 滚动到底部/右边，会触发 scrolltolower 事件
    handleLower() {
      this.params.skip += this.params.limit
      if (this.hasMore) {
        this.getList()
      } else {
        uni.showToast({
          title: '已经是最后一页了',
          icon: 'none'
        })
      }
    },
    getList() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
        data: this.params
      }).then(result => {
        // 判断是否还有下一页
        if (!result.res.vertical.length) {
          uni.showToast({
            title: '已经是最后一页了',
            icon: 'none'
          })
          return this.hasMore = false
        }
        if (!this.recommendList.length) {
          //   头部推荐列表
          this.recommendList = result.res.homepage[1].items
          this.monthList = result.res.homepage[2]
          this.monthList.MM = moment(this.monthList.stime).format('MM')
          this.monthList.DD = moment(this.monthList.stime).format('DD')
        }
        this.hotList = [...this.hotList, ...result.res.vertical]
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.homePage {
  height: calc(100vh - 72rpx);
  .recommend {
    display: flex;
    flex-wrap: wrap;
    .recommendItem {
      width: 50%;
      border: 5rpx solid #fff;
    }
  }
  .month_wrap {
    .title {
      display: flex;
      justify-content: space-between;
      padding-top: 20rpx;
      .title_left {
        display: flex;
        margin-left: 10rpx;
        font-weight: 600;
        .title_left_date {
          color: $color;
          font-size: 20rpx;
          .title_left_date_month {
            font-size: 30rpx;
          }
        }
        .title_left_info {
          color: 666;
          margin-left: 15rpx;
        }
      }
      .title_right {
        color: $color;
        font-size: 24rpx;
        margin-right: 10rpx;
      }
    }
    .monthContent {
      display: flex;
      flex-wrap: wrap;
      .monthContentItem {
        width: 33.33%;
        border: 5rpx solid #fff;
      }
    }
  }
  .hot {
    .hot_title {
      padding: 20rpx 0 20rpx 10rpx;
      text {
        border-left: 16rpx solid $color;
        font-size: 28rpx;
        font-weight: 600;
      }
    }
    .hot_content {
      display: flex;
      flex-wrap: wrap;
      .hot_contentItem {
        width: 33.33%;
        border: 5rpx solid #fff;
      }
    }
  }
}
</style>
