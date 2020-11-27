<template>
  <view class="home">
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
      <view v-if="current === 0">
        <HomeRecommend></HomeRecommend>
      </view>
      <view v-if="current === 1">
        <HomeCategory></HomeCategory>
      </view>
      <view v-if="current === 2">
        <HomeNew></HomeNew>
      </view>
      <view v-if="current === 3">
        <HomeAlbum></HomeAlbum>
      </view>
    </view>
  </view>
</template>

<script>
import { uniSegmentedControl } from '@dcloudio/uni-ui'
import HomeRecommend from './homeRecommend.vue'
import HomeCategory from './homeCategory.vue'
import HomeNew from './homeNew.vue'
import HomeAlbum from './homeAlbum.vue'
export default {
  data() {
    return {
      items: [{
        title: '推荐'
      },
      {
        title: '分类'
      },
      {
        title: '最新'
      },
      {
        title: '专辑'
      }
      ],
      current: 0
    }
  },
  components: {
    uniSegmentedControl,
    HomeRecommend,
    HomeCategory,
    HomeNew,
    HomeAlbum
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }
    }
  }
}
</script>

<style lang="scss">
.home {
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
