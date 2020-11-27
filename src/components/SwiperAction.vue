<template>
  <view
    @touchstart='handleTouchstart'
    @touchend='handleTouchend'
  >
    <slot></slot>
  </view>
</template>

<script>
export default {
  data() {
    return {
      startTime: 0,
      clientX: 0,
      clientY: 0
    }
  },
  methods: {
    handleTouchstart(event) {
      this.clientX = event.changedTouches[0].clientX
      this.clientY = event.changedTouches[0].clientY
      this.startTime = Date.now()
    },
    handleTouchend(event) {
      let endTime = Date.now()
      let endClientX = event.changedTouches[0].clientX
      let endClientY = event.changedTouches[0].clientY
      if (endTime - this.startTime > 1000) {
        return
      }
      if (Math.abs(endClientX - this.clientX) > 10 && Math.abs(endClientY - this.clientY) < 10) {
        let direction = endClientX - this.clientX > 0 ? 'left' : 'right'
        this.$emit('direction', direction)
      } else {
        return
      }
    }
  }
}
</script>

<style lang="scss" scoped>
</style>