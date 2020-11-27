<template>
  <view class="video_play">
    <img :src="videoInfo.img">
    <view class="video_play_tools">
      <view
        :class="['iconfont',muted?'iconjingyin':'iconshengyin']"
        @click="handleMuted"
      ></view>
      <view class="iconfont iconzhuanfa">
        <button open-type="share"></button>
      </view>
    </view>
    <view class="video_wrap">
      <video
        :src="videoInfo.video"
        object-fit="fill"
        :muted="muted"
      ></video>
    </view>
    <view class="download_wrap">
      <view
        class="download"
        @click="downLoadVideo"
      >
        下载高清
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      videoInfo: {},
      muted: false  //是否静音播放
    }
  },
  onLoad() {
    console.log(getApp().globalData.videoItem);
    this.videoInfo = getApp().globalData.videoItem
  },
  methods: {
    handleMuted() {
      this.muted = !this.muted
    },
    async downLoadVideo() {
      uni.showLoading({
        title: '下载中'
      })
      const result = await uni.downloadFile({
        url: this.videoInfo.video
      })
      console.log(result);
      const { tempFilePath } = result[1]

      await uni.saveVideoToPhotosAlbum({
        filePath: tempFilePath
      })

      uni.hideLoading()
      uni.showToast({
        title: '视频保存至相册中',
        icon: 'success'
      })
    }
  },
}
</script>

<style lang="scss" scoped>
.video_play {
  position: relative;
  img {
    width: 100vh;
    height: 100vh;
    position: absolute;
    z-index: -1;
    // css3滤镜
    filter: blur(40rpx);
  }
  .video_play_tools {
    height: 80rpx;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    padding-top: 10rpx;
    .iconfont {
      width: 80rpx;
      height: 80rpx;
      font-size: 50rpx;
      color: #fff;
      border-radius: 50%;
      background-color: rgba(0, 0, 0, 0.25);
      display: flex;
      justify-content: center;
      align-items: center;
      margin-right: 16rpx;
    }
    .iconzhuanfa {
      position: relative;
      button {
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
  .download_wrap {
    display: flex;
    justify-content: center;
    margin-top: 26rpx;
    .download {
      width: 360rpx;
      height: 80rpx;
      border: 2rpx solid #fff;
      background-color: rgba(0, 0, 0, 0.25);
      line-height: 80rpx;
      color: #fff;
      border-radius: 40rpx;
      text-align: center;
    }
  }
}
</style>