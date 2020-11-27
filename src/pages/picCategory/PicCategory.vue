<template>
  <view class="pic_category">
    <view class="top">
      <view class="top-inner">
        <uni-segmented-control
          :current="current"
          :values="items"
          @clickItem="onClickItem"
          style-type="text"
          active-color="#d4237a"
        ></uni-segmented-control>
      </view>
      <view class="iconfont iconsearch"></view>

      <scroll-view
        class="content"
        scroll-y
        @scrolltolower="handelToLower"
      >
        <view class="new_pic">
          <view
            class="newItem"
            v-for="(item,index) in vertical"
            :key="item.id"
          >
            <ToDetail
              :list="vertical"
              :index="index"
            >
              <img :src="item.thumb">
            </ToDetail>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
import { uniSegmentedControl } from '@dcloudio/uni-ui'
import ToDetail from '../../components/ToDetail'
export default {
  data() {
    return {
      items: ['最新', '热门'],
      current: 0,
      order: ['new', 'hot'],
      id: 0,
      params: {
        limit: 30,
        skip: 0,
        order: ''
      },
      vertical: [],
      hasMore: true
    }
  },
  components: {
    uniSegmentedControl,
    ToDetail
  },
  onLoad(options) {
    this.id = options.id
    this.getPic()
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;

        // 换标题 清空请求参数
        this.params.skip = 0
        this.vertical = []
        this.getPic()
      }
    },
    getPic() {
      this.params.order = this.order[this.current]
      this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params
      }).then(request => {
        if (request.res.vertical.length === 0) {
          uni.showToast({
            title: '我是有底线的',
            icon: 'none'
          })
          this.hasMore = false
          return
        }
        this.vertical = [...this.vertical, ...request.res.vertical]
      })
    },
    handelToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit
        this.getPic()
      } else {
        uni.showToast({
          title: '我是有底线的',
          icon: 'none'
        })
      }
    }
  },
}
</script>

<style lang="scss" scoped>
.pic_category {
  padding-top: 10rpx;
  .top {
    position: relative;
    .top-inner {
      width: 60%;
      margin: 0 auto;
    }
  }
  .iconsearch {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    right: 7%;
  }
  .content {
    height: calc(100vh - 72rpx);
    .new_pic {
      display: flex;
      flex-wrap: wrap;
      .newItem {
        width: 33.33%;
        border: 4rpx solid #fff;
      }
    }
  }
}
</style>