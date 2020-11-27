<template>
  <scroll-view
    scroll-y
    @scrolltolower="handleToLower"
    class="albumScroll"
  >
    <view class="album">
      <swiper
        class="album_banner"
        indicator-dots
        circular
        autoplay
        interval="1500"
      >
        <swiper-item
          class="album_bannerItem"
          v-for="item in bannerList"
          :key="item.id"
        >
          <img :src="item.thumb">
        </swiper-item>
      </swiper>

      <view class="album_list">
        <navigator
          class="album_listItem"
          v-for="item in albumList "
          :key="item.id"
          :url="`/pages/album/album?id=${item.id}`"
        >
          <view class="leftImg">
            <img
              :src="item.cover"
              mede="aspectFill"
            >
          </view>
          <view class="rightContent">
            <view class="albumName">{{item.name}}</view>
            <view class="albumDesc">{{item.desc}}</view>
            <view class="albumAttention">
              <view>+ 关注</view>
            </view>
          </view>
        </navigator>
      </view>
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 10,
        order: 'new',
        skip: 0
      },
      bannerList: [],
      albumList: [],
      hasMore: true
    }
  },
  mounted() {
    uni.setNavigationBarTitle({
      title: '专辑'
    });

    this.getList()
  },
  methods: {
    getList() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v1/wallpaper/album',
        data: this.params
      }).then(result => {
        if (!result.res.album.length) {
          uni.showToast({
            title: '已经是最后一页了',
            icon: 'none'
          })
          return this.hasMore = false
        }
        if (!this.bannerList.length) {
          this.bannerList = result.res.banner
        }
        this.albumList = [...this.albumList, ...result.res.album]
      })
    },
    handleToLower() {
      if (this.hasMore) {
        this.params.skip += this.params.limit
        this.getList()
      } else {
        uni.showToast({
          title: '已经是最后一页了',
          icon: 'none'
        })
      }

    }
  },
}
</script>

<style lang="scss" scoped>
.albumScroll {
  height: calc(100vh - 72rpx);
}
.album {
  .album_banner {
    height: calc(750rpx / 2.5);
    .album_bannerItem {
      img {
        height: 100%;
      }
    }
  }
  .album_list {
    padding-top: 10rpx;
    .album_listItem {
      padding: 10rpx;
      display: flex;
      .leftImg {
        flex: 1;
        img {
          width: 200rpx;
          height: 200rpx;
        }
      }
      .rightContent {
        flex: 2;
        overflow: hidden;
        .albumName {
          font-size: 30rpx;
          color: #000;
          padding: 10rpx 0;
        }
        .albumDesc {
          font-size: 24rpx;
          padding: 10rpx 0;

          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
        }
        .albumAttention {
          padding: 10rpx 10rpx 10rpx 0;
          display: flex;
          justify-content: flex-end;
          view {
            border: 1rpx solid $color;
            color: $color;
            padding: 5rpx 10rpx;
          }
        }
      }
    }
  }
}
</style>
