<template>
  <view>
    <scroll-view
      class="video"
      scroll-y
      enable-flex
      @scrolltolower="handleToLower"
    >
      <view
        class="videoItem"
        v-for="item in videoList"
        :key="item.id"
        @click="goVideoPlay(item)"
      >
        <img
          :src="item.img"
          mode="widthFix"
        >
      </view>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      videoList: [],
      hasMore: true,
    }
  },
  props: {
    urlObj: {
      type: Object,
    }
  },
  watch: {
    urlObj(newValue, oldValue) {
      this.videoList = []
      this.getVideo()
    }
  },
  mounted() {
    this.getVideo()
  },
  methods: {
    getVideo() {
      this.request({
        url: this.urlObj.url,
        data: this.urlObj.params
      }).then(result => {
        console.log(result);
        if (result.res.videowp.length === 0) {
          this.hasMore = false
          uni.showToast({
            title: '我是有底线的',
            icon: 'none'
          })
          return
        }
        this.videoList = [...this.videoList, ...result.res.videowp]
      })
    },
    handleToLower() {
      if (this.hasMore) {
        this.urlObj.params.skip += this.urlObj.params.limit
        this.getVideo()
      } else {
        uni.showToast({
          title: '我是有底线的',
          icon: 'none'
        })
      }
    },
    goVideoPlay(videoItem) {
      getApp().globalData.videoItem = videoItem
      console.log('111');
      uni.navigateTo({
        url: '/pages/videoPlay/videoPlay'
      })
    }
  },
}
</script>

<style lang="scss" scoped>
.video {
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 72rpx);
  .videoItem {
    width: 33.33%;
    border: 4rpx solid #fff;
  }
}
</style>