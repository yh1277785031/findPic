<template>
  <view @click="handleClick">
    <slot></slot>
  </view>
</template>

<script>
export default {
  props: {
    list: {
      type: Array
    },
    index: {
      type: Number
    }
  },
  methods: {
    handleClick() {
      // 将数据保存在全局中
      getApp().globalData.list = this.list
      getApp().globalData.index = this.index
      //   跳转到图片详情页
      uni.navigateTo({
        url: '/pages/picDetail/picDetail'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
</style>