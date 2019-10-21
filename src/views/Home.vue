<template>
  <div class="content">
    <div class="home" id="container"></div>
    <div class="input-card" style="width:24rem;right: 17rem;">
      <h4>添加、删除覆盖物</h4>
      <div class="input-item">
        <label>Marker：</label>
        <button class="btn" id="add-marker" style="margin-right:1rem;">添加Marker</button>
        <button class="btn" id="remove-marker">删除Marker</button>
      </div>
      <div class="input-item">
        <label>Circle：</label>
        <button class="btn" id="add-circle" style="margin-right:1rem;">添加Circle</button>
        <button class="btn" id="remove-circle">删除Circle</button>
      </div>
    </div>
    <div class="input-card" style="width:15rem">
      <h4>官方默认自定义样式</h4>
      <div id="map-styles">
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('normal')" value="normal" />
          <span>标准</span>
          <span class="input-text">normal</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('dark')" value="dark" />
          <span>幻影黑</span>
          <span class="input-text">dark</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('light')" value="light" />
          <span>月光银</span>
          <span class="input-text">light</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('whitesmoke')" value="whitesmoke" checked />
          <span>远山黛</span>
          <span class="input-text">whitesmoke</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('fresh')" value="fresh" />
          <span>草色青</span>
          <span class="input-text">fresh</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('grey')" value="grey" />
          <span>雅士灰</span>
          <span class="input-text">grey</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('graffiti')" value="graffiti" />
          <span>涂鸦</span>
          <span class="input-text">graffiti</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('macaron')" value="macaron" />
          <span>马卡龙</span>
          <span class="input-text">macaron</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('blue')" value="blue" />
          <span>靛青蓝</span>
          <span class="input-text">blue</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('darkblue')" value="darkblue" />
          <span>极夜蓝</span>
          <span class="input-text">darkblue</span>
        </div>
        <div class="input-item">
          <input type="radio" name="mapStyle" @click="changeTheme('wine')" value="wine" />
          <span>酱籽</span>
          <span class="input-text">wine</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import area from '@/mock/area'
const { data } = area

export default {
  name: 'home',
  mounted () {
    this.$nextTick().then(_ => {
      this.initMap()
    })
  },
  methods: {
    initMap () {
      // eslint-disable-next-line no-undef
      this.map = new AMap.Map('container', {
        center: [114.12744, 22.64469],
        zoom: 11,
        pitch: 50,
        // 使用3D视图
        viewMode: '3D'
      })

      // this.drawAreaLine()

      // 加载天气查询插件
      // eslint-disable-next-line no-undef
      AMap.plugin(['AMap.Weather', 'AMap.DistrictSearch'], () => {
        // 创建天气查询实例
        // eslint-disable-next-line no-undef
        const weather = new AMap.Weather()
        // 执行实时天气信息查询
        weather.getLive('深圳市', (err, data) => {
          console.log(err, data)
        })

        // 设置光照
        // eslint-disable-next-line no-undef
        this.map.AmbientLight = new AMap.Lights.AmbientLight([1, 1, 1], 0.5)
        // eslint-disable-next-line no-undef
        this.map.DirectionLight = new AMap.Lights.DirectionLight([0, 0, 1], [1, 1, 1], 1)

        // eslint-disable-next-line no-undef
        const object3Dlayer = new AMap.Object3DLayer()
        this.map.add(object3Dlayer)

        // eslint-disable-next-line no-undef
        this.DistrictSearch = new AMap.DistrictSearch({
          subdistrict: 0, // 返回下一级行政区
          extensions: 'all', // 返回行政区边界坐标组等具体信息
          level: 'city' // 查询行政级别为 市
        }).search('深圳市', (status, result) => {
          const bounds = result.districtList[0].boundaries
          const height = 5000
          const color = '#0088ffcc' // rgba
          // eslint-disable-next-line no-undef
          const prism = new AMap.Object3D.Prism({
            path: bounds,
            height: height,
            color: color
          })

          prism.transparent = true
          object3Dlayer.add(prism)

          // eslint-disable-next-line no-undef
          const text = new AMap.Text({
            text: result.districtList[0].name + '</br>(' + result.districtList[0].adcode + ')',
            verticalAlign: 'bottom',
            position: [116.528261, 39.934313],
            height: 5000,
            style: {
              'background-color': 'transparent',
              '-webkit-text-stroke': 'red',
              '-webkit-text-stroke-width': '0.5px',
              'text-align': 'center',
              'border': 'none',
              'color': 'white',
              'font-size': '24px',
              'font-weight': 600
            }
          })

          text.setMap(this.map)
        })
      })
    },
    drawAreaLine () {
      const lineDate = []
      let num = 0
      data.map((v1, i1) => {
        v1.points.map((v2, i2) => {
          lineDate[num] = []
          v2.map((v3, i3) => {
            // eslint-disable-next-line no-undef
            lineDate[num].push(new AMap.LngLat(v3.lng, v3.lat))
          })
          num++
        })
      })
      const lineObject = {}
      // 绘制所有的行政区
      lineDate.map((v, i) => {
        // eslint-disable-next-line no-undef
        // lineObject[i] = new AMap.Polyline(v, { strokeColor: '#39efe3', fillColor: '#4b93ff', fillOpacity: 0.1, strokeWeight: 1, strokeOpacity: 1 }) // 建立多边形覆盖物
        // eslint-disable-next-line no-undef
        lineObject[i] = new AMap.Polyline({
          path: v,
          borderWeight: 2, // 线条宽度，默认为 1
          strokeColor: '#4b93ff', // 线条颜色
          lineJoin: 'round' // 折线拐点连接处样式
        }) // 建立多边形覆盖物
        // lineObject[i].disableMassClear()
        this.map.add(lineObject[i])
      })
    },
    changeTheme (value) {
      console.log(value)

      const styleName = 'amap://styles/' + value
      this.map.setMapStyle(styleName)
    }
  },
  beforeDestroy () {
    if (this.map) this.map.destroy()
  }
}
</script>

<style lang="scss" scoped>
.home,
.content {
  width: 100%;
  height: 100vh;
}
</style>
