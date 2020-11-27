<template>
  <view>
    <view class="category">
      <navigator
        class="categoryItem"
        v-for="item in categoryList"
        :key="item.id"
        :url="`/pages/picCategory/PicCategory?id=${item.id}`"
      >
        <img :src="item.cover">
        <view>{{item.name}}</view>
      </navigator>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      categoryList: []
    }
  },
  mounted() {
    uni.setNavigationBarTitle({
      title: '分类'
    });
    this.request({
      url: 'http://157.122.54.189:9088/image/v1/vertical/category'
    }).then((result) => {
      console.log(result);
      this.categoryList = result.res.category
    })
  },
}
</script>

<style lang="scss" scoped>
.category {
  display: flex;
  flex-wrap: wrap;
  .categoryItem {
    width: 33.33%;
    position: relative;
    border: 5rpx solid #fff;
    img {
      height: 240rpx;
    }
    view {
      height: 50rpx;
      width: 100%;
      position: absolute;
      font-size: 40rpx;
      color: #fff;
      left: 0;
      bottom: 10rpx;
      padding-left: 20rpx;
      display: flex;
      align-items: center;
      // css3渐变
      background-image: linear-gradient(
        to right top,
        rgba(0, 0, 0, 0.2),
        rgba(0, 0, 0, 0)
      );
    }
  }
}
</style>
