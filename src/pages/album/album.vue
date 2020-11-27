<template>
  <view>
    <!-- 封面 -->
    <view class="album_top">
      <view>
        <img
          :src="album.cover"
          mode="widthFix"
        >
        <view
          class="title"
          v-if="album.name"
        >
          <view class="title_left">{{album.name}}</view>
          <view class="title_right">关注专辑</view>
        </view>
      </view>
    </view>
    <!-- 作者 -->
    <view
      class="album_author"
      v-if="album.user.name"
    >
      <view class="album_author_info">
        <img :src="album.user.avatar">
        <view>{{album.user.name}}</view>
      </view>
      <view class="album_desc">
        <text>
          {{album.desc}}
        </text>
      </view>
    </view>
    <!-- 专辑列表 -->
    <view class="album_list">
      <view
        class="album_listItem"
        v-for="(item,index) in wallpaperList"
        :key="item.id"
      >
        <ToDetail
          :index="index"
          :list="wallpaperList"
        >
          <img
            mode="aspectFill"
            :src="item.thumb+item.rule.replace('$<Height>',360)"
          >
        </ToDetail>
      </view>
    </view>
  </view>
</template>

<script>
import { ToDetail } from "../../components/ToDetail";
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: 'new',
        skip: 0,
        // “1” 表示第一次请求 “0”表示不是第一次请求
        first: 1
      },
      albumID: 0,
      album: {},
      wallpaperList: [],
      hasMore: true
    }
  },
  components: {
    ToDetail,
  },
  methods: {
    getAlbumList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.albumID}/wallpaper`,
        data: this.params
      }).then(result => {
        if (Object.keys(this.album).length === 0) {
          this.album = result.res.album
          this.params.first = 0
        }
        if (result.res.wallpaper.length === 0) {
          uni.showToast({
            title: '我是有底线的',
            icon: "none"
          })
          this.hasMore = false
          return
        }
        this.wallpaperList = [...this.wallpaperList, ...result.res.wallpaper]
      })
    }
  },
  onLoad(options) {
    console.log(options);
    this.albumID = options.id
    // this.albumID = '5d6dd449e7bce75a8ee33c03'
    this.getAlbumList()
  },
  onReachBottom() {
    this.params.skip += this.params.limit
    if (this.hasMore) {
      this.getAlbumList()
    } else {
      uni.showToast({
        title: '我是有底线的',
        icon: "none"
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.album_top {
  position: relative;
  .title {
    width: 100%;
    display: flex;
    position: absolute;
    color: #fff;
    bottom: 10%;
    align-items: center;
    justify-content: space-between;
    padding-left: 20rpx;
    padding-right: 14rpx;
    .title_left {
      font-size: 36rpx;
      font-weight: 500;
    }
    .title_right {
      background-color: $color;
      font-size: 24rpx;
      padding: 10rpx 20rpx;
      border-radius: 4rpx;
    }
  }
}
.album_author {
  padding: 16rpx;
  .album_author_info {
    display: flex;
    align-items: center;
    margin-bottom: 10rpx;
    img {
      width: 60rpx;
      height: 60rpx;
      margin-right: 16rpx;
    }
    view {
      color: #000;
      font-weight: 600;
    }
  }
}
.album_list {
  display: flex;
  flex-wrap: wrap;
  .album_listItem {
    width: 33.33%;
    border: 4rpx solid #fff;
    img {
      height: 160rpx;
    }
  }
}
</style>