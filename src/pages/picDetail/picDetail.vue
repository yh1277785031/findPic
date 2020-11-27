<template>
  <view>
    <!-- 用户信息 -->
    <view
      class="user_info"
      v-if="picDetail.isUser"
    >
      <view class="user_icon"><img
          :src="picDetail.user.avatar"
          mode="widthFix"
        ></view>
      <view class="user_desc">
        <view class="user_name">{{picDetail.user.name}}</view>
        <view class="user_time">{{picDetail.cnTime}}</view>
      </view>
    </view>
    <!-- 图片 -->
    <view>
      <SwiperAction @direction="handleDirection">
        <img
          :src="picDetail.thumb"
          mode="widthFix"
        >
      </SwiperAction>
    </view>
    <!-- 点赞收藏 -->
    <view class="pic_like">
      <view class="dianzan">
        <text class="iconfont icondianzan"></text>
        <text>{{picDetail.rank}}</text>
      </view>
      <view class="shoucang">
        <text class="iconfont iconshoucang"></text>
        <text>收藏</text>
      </view>
    </view>
    <!-- 评论列表 -->
    <view
      class="hot_comment"
      v-if="hotComments.length>0"
    >
      <view class="comment_title">
        <text class="iconfont iconhot1"></text>
        <text>最热评论</text>
      </view>
      <view
        class="comment_list"
        v-for="item in hotComments"
        :key="item.id"
      >
        <view class="commentator">
          <view class="commentator_avatar">
            <img
              :src="item.user.avatar"
              mode="widthFix"
            >
          </view>
          <view class="commentator_info">
            <view class="commentator_name">{{item.user.name}}</view>
            <view class="commentator_time">{{item.cnTime}}</view>
          </view>
        </view>
        <view class="comment_content">
          <view>{{item.content}}</view>
          <view>
            <text class="iconfont icondianzan"></text>
            <text>{{item.size}}</text>
          </view>
        </view>
      </view>
    </view>
    <view
      class="hot_comment"
      v-if="newComments.length>0"
    >
      <view class="comment_title">
        <text class="iconfont iconpinglun"></text>
        <text>最新评论</text>
      </view>
      <view
        class="comment_list"
        v-for="item in newComments"
        :key="item.id"
      >
        <view class="commentator">
          <view class="commentator_avatar">
            <img
              :src="item.user.avatar"
              mode="widthFix"
            >
          </view>
          <view class="commentator_info">
            <view class="commentator_name">{{item.user.name}}</view>
            <view class="commentator_time">{{item.cnTime}}</view>
          </view>
        </view>
        <view class="comment_content">
          <view>{{item.content}}</view>
          <view>
            <text class="iconfont icondianzan"></text>
            <text>{{item.size}}</text>
          </view>
        </view>
      </view>
    </view>
    <!-- 下载图片 -->
    <view
      class="download"
      @click="handleDownLoad"
    >
      <view class="download_btn">下载图片</view>
    </view>
  </view>
</template>

<script>
import moment from "moment";
import SwiperAction from '../../components/SwiperAction'
moment.locale("zh-cn")
export default {
  data() {
    return {
      // 图片索引
      index: 0,
      picDetail: {
      },
      hotComments: [],
      newComments: [],
      direction: '',
    }
  },
  components: {
    SwiperAction,
  },
  onLoad() {
    const { index } = getApp().globalData
    this.index = index
    this.getData()
  },

  methods: {
    getData() {
      const { list } = getApp().globalData
      this.picDetail = list[this.index]
      // this.picDetail.thumb = this.picDetail.thumb + this.picDetail.rule.replace('$<Height>', 360)
      // xx年前的数据
      this.picDetail.cnTime = moment(this.picDetail.atime).fromNow()
      console.log(this.picDetail.user)
      if (this.picDetail.user) {
        this.picDetail.isUser = true
      } else {
        this.picDetail.isUser = false
      }
      this.getComments(this.picDetail.id)
    },
    getComments(id) {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
      }).then(result => {
        console.log(result);
        result.res.hot.forEach(item => {
          item.cnTime = moment(item.atime).fromNow()
        });
        result.res.comment.forEach(item => {
          item.cnTime = moment(item.atime).fromNow()
        });
        this.hotComments = result.res.hot

        this.newComments = result.res.comment
      })
    },
    handleDirection(dir) {
      const { list } = getApp().globalData
      console.log(dir);
      // 用户左滑，图片向右
      if (dir == 'right' && this.index < list.length - 1) {
        this.index = this.index + 1
        this.getData()
      } else if (dir == 'left' && this.index > 0) {
        // 用户有滑，图片向左
        this.index = this.index - 1
        this.getData()
      } else if (dir == 'left' && this.index == 0) {
        uni.showToast({
          title: '已经是第一张图片了',
          icon: 'none'
        })
      } else {
        uni.showToast({
          title: '已经是最后一张图片了',
          icon: 'none'
        })
      }
    },
    async handleDownLoad() {
      uni.showLoading({
        title: '下载中'
      })
      const result1 = await uni.downloadFile({
        url: this.picDetail.img
      })
      const { tempFilePath } = result1[1]

      uni.saveImageToPhotosAlbum({
        filePath: tempFilePath
      }).then(result => {
        console.log(result);
        uni.hideLoading()
        uni.showToast({
          title: '图片保存至相册',
          icon: 'success'
        })
      })
    }
  }
}
</script>

<style lang="scss">
.user_info {
  padding: 40rpx 40rpx 20rpx;
  display: flex;
  align-items: center;
  .user_icon {
    img {
      width: 70rpx;
      border-radius: 50%;
    }
  }
  .user_desc {
    margin-left: 20rpx;
    .user_name {
      font-size: 30rpx;
      font-weight: 600;
      padding-bottom: 6rpx;
    }
    .user_time {
      font-size: 20rpx;
      color: #ccc;
    }
  }
}
.pic_like {
  display: flex;
  background-color: #fcfffc;
  align-items: center;
  justify-content: space-around;
  border-bottom: 2rpx solid #eee;
  height: 80rpx;
}
.hot_comment {
  padding-top: 10rpx;
  .comment_title {
    padding-left: 10rpx;
    display: flex;
    align-items: center;
    padding-bottom: 16rpx;
    .iconhot1 {
      color: #ff1c05;
      font-size: 54rpx;
    }
    .iconpinglun {
      color: #92b9d6;
      font-size: 54rpx;
    }
    text {
      padding-left: 10rpx;
      font-size: 24rpx;
      font-weight: 600;
      color: #7c6160;
    }
  }
  .comment_list {
    padding-left: 10rpx;
    .commentator {
      display: flex;
      align-items: center;
      padding-bottom: 20rpx;
      .commentator_avatar {
        width: 80rpx;
      }
      .commentator_info {
        padding-left: 10rpx;
        .commentator_name {
          color: #ccc;
        }
      }
    }
    .comment_content {
      display: flex;
      justify-content: space-between;
      color: #000;
      font-weight: 600;
      margin: 0 80rpx;
      padding-bottom: 20rpx;
      border-bottom: 6rpx solid #eee;
    }
  }
}
.download {
  display: flex;
  height: 120rpx;
  align-items: center;
  justify-content: center;
  .download_btn {
    height: 70%;
    width: 90%;
    color: #fff;
    font-size: 50rpx;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: $color;
  }
}
</style>
