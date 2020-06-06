<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'

  export default {
    name: "Scroll",
    props: {
      probeType: {
        type: Number,
        default: 0
      },
      pullUpLoad: {
        type: Boolean,
        default: true
      }
    },
    data() {
      return {
        scroll: {}
      }
    },
    mounted() {
      this.scroll = new BScroll(this.$refs.wrapper, {
        scrollY: true,
        click: true,
        probeType: this.probeType,
        pullUpLoad: this.pullUpLoad,
      })
      // 2.将监听事件回调
      this.scroll.on('scroll', pos => {
        this.$emit('scroll', pos)
      })

      this.scroll.on('pullingUp', () => {
        this.$emit('pullingUp')
      })
    },
    methods: {
      scrollTo(x, y, time) {
        this.scroll.scrollTo(x, y, time)
      },
      finishPullUp() {
        this.scroll.finishPullUp()
      }
    },
  }
</script>

<style scoped>

</style>