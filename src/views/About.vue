<template>
  <div class="about">
    <CubeOverlay visible.sync="visibleLayout">
      <template v-if="visibleLayout">
        <div>
          <h1> 自定义组件  </h1>
        </div>
      </template>
    </CubeOverlay>

     <el-button type="primary" size="small" @click="openOverlay">弹层1</el-button>
     <el-button type="primary" size="small" @click="openOverlay">弹层2</el-button>
     <el-button type="primary" size="small" @click="visibleLayout=false">关闭</el-button>
  </div>
</template>

<script>
import CubeOverlay from '../components/CubeOverlay'
export default {
  name: 'smsmms',
  components: {
    CubeOverlay
  },
  data () {
    return {
      visibleLayout: false
    }
  },
  mounted () {

  },
  methods: {
    openOverlay () {
      this.visibleLayout = false
      setTimeout(() => {
        this.visibleLayout = true
      }, 200)
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
