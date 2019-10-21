<template>
  <div class="CubeOverlay">
    <i class="el-icon-close" @click.stop="removeCubeOverlay" />
    <slot />
  </div>
</template>

<script>
export default {
  name: 'CubeOverlay',
  props: {
    pane: {
    // floatPane、markerMouseTarget、floatShadow、labelPane、markerPane、markerShadow、mapPane
      type: String,
      default: () => 'labelPane'
    },
    point: {
      type: [String, Object, Array],
      default: () => ''
    },
    visible: {
      type: Boolean,
      default: () => false
    }
  },
  data () {
    return {
      myCompOverlay: null
    }
  },
  watch: {
    point: {
      handler (value) {
        if (value) {
          this.$nextTick().then(_ => {
            this.init()
            // 暂时不设置参数 提供坐标改变通知
            this.$emit('pointChange')
          })
        } else {
          // this.removeCubeOverlay()
        }
      }
    }
  },
  mounted () {
    this.$nextTick().then(_ => {
      this.init()
      console.log('mounted ++++ ')
    })
  },
  beforeDestroy () {
    this.$emit('VueOverlayBeforeDestroy')
    console.log('组件销毁')
  },
  methods: {
    init () {
      console.log('初始化')
      this.myCompOverlay = true
    },
    update () {

    },
    removeCubeOverlay () {
      const { map } = this
      if (this.myCompOverlay) {
        map.removeOverlay(this.myCompOverlay)
        this.$emit('update:visible', false)
        this.$emit('close', false)
      }
    },
    showOverlay () {
      console.log('显示')
    },
    hiedOverlay () {
      console.log('隐藏')
    }
  }
}
</script>

<style lang="scss" scoped>
.CubeOverlay{
  .el-icon-close{
    color: #ffffff;
    top: 22px;
    right: 24px;
    position: absolute;
    font-size: 30px;
    z-index: 999999;
    font-weight: 600;
    display: inline-block;
    cursor: pointer;
  }
}
</style>
