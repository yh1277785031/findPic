<template>
  <view class="video">
    <view class="top">
      <view class="top-inner">
        <uni-segmented-control
          :current="current"
          :values="items.map(item=>item.title)"
          @clickItem="onClickItem"
          style-type="text"
          active-color="#d4237a"
        ></uni-segmented-control>
      </view>
      <view class="iconfont iconsearch"></view>
    </view>

    <view class="content">
      <view v-if="current < 4">
        <VideoMain :urlObj={url:this.items[this.current].url,params:this.items[this.current].params}></VideoMain>
      </view>
      <view v-if="current == 4">
        <VideoCategory></VideoCategory>
      </view>
    </view>
  </view>
</template>

<script>
import { uniSegmentedControl } from '@dcloudio/uni-ui'
import { VideoCategory } from "./videoCategory";
import { VideoMain } from "./videoMain";
export default {
  data() {
    return {
      items: [{
        title: '推荐',
        url: 'http://157.122.54.189:9088/videoimg/v1/videowp/featured',
        params: { limit: 30, skip: 0, order: 'hot' }
      },
      {
        title: '娱乐',
        url: 'http://157.122.54.189:9088/videoimg/v1/videowp/category/59b25abbe7bce76bc834198a',
        params: { limit: 30, skip: 0, order: 'new' }
      },
      {
        title: '最新',
        url: 'http://157.122.54.189:9088/videoimg/v1/videowp/videowp',
        params: { limit: 30, skip: 0, order: 'new' }
      },
      {
        title: '热门',
        url: 'http://157.122.54.189:9088/videoimg/v1/videowp/videowp',
        params: { limit: 30, skip: 0, order: 'hot' }
      },
      {
        title: '分类',
        url: 'http://157.122.54.189:9088/videoimg/v1/videowp/category'
      }
      ],
      current: 0
    }
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }
    }
  },
  components: {
    uniSegmentedControl,
    VideoCategory,
    VideoMain
  },
}
</script>

<style lang="scss" scoped>
.video {
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
}
</style>
