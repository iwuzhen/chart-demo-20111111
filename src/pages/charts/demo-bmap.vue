<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onBeforeMount, onMounted } from 'vue'
import 'd3-array'
// import { convertData, scale } from '~/hook/utils'
// import ygk from '~/assert/ygk.png'
import mapBackground from '~/assert/mapBackground.png'

import 'echarts/extension/bmap/bmap'

// echarts.registerMap('USA', chinaGeo, {
//   'Alaska': {
//     // 把阿拉斯加移到美国主大陆左下方
//     left: -131,
//     top: 25,
//     width: 15,
//   },
//   'Hawaii': {
//     left: -110,
//     top: 28,
//     width: 5,
//   },
//   'Puerto Rico': {
//     // 波多黎各
//     left: -76,
//     top: 26,
//     width: 2,
//   },
// })
onBeforeMount(() => {
  const recaptchaScript = document.createElement('script')
  recaptchaScript.setAttribute('src', 'https://api.map.baidu.com/api?v=3.0&ak=bDippwLGB3vf7hgOZLadOKnV')
  document.head.appendChild(recaptchaScript)
})

onMounted(async () => {
  let response = await fetch('/data/test_1.json')
  echarts.registerMap('testMap', await response.json())

  response = await fetch('/data/sfba.json')
  echarts.registerMap('sfbaMap', await response.json())

  response = await fetch('/data/100000_full.json')
  echarts.registerMap('China', await response.json())

  const elem = document.getElementById('chartID')
  // let chartObj = echarts.init(elem);
  if (!elem)
    return

  const chartObj = echarts.init(elem, undefined, { renderer: 'svg' })
  const colors = ['#00F8FF', '#00FF00', '#FFF800', '#FF0000']
  const getColor = (value) => {
    if (value <= 10)
      return colors[0]

    else if (value > 10 && value <= 20)
      return colors[1]

    else if (value > 20 && value <= 50)
      return colors[2]

    else
      return colors[3]
  }

  const data = [
    [113.26388, 23.12946, -106.547764, 29.565907, '广州', 60],
    [108.939839, 34.34127, 106.547764, 29.565907, '西安', 5],
    [113.624931, 34.74725, 106.547764, 29.565907, '郑州', 12],
    [115.857941, 28.68202, 106.547764, 29.565907, '南昌', 10],
    [110.199889, 20.044221, 106.547764, 29.565907, '海口', 5],
    [121.47375, 31.23026, 106.547764, 29.565907, '上海', 62],
    [125.32357, 43.81602, 106.547764, 29.565907, '长春', 18],
    [117.22901, 31.820571, 106.547764, 29.565907, '合肥', 18],
    [114.514299, 38.04276, 106.547764, 29.565907, '石家庄', 18],
    [117.199371, 39.0851, 106.547764, 29.565907, '天津', 18],
    [106.550729, 29.564709, 106.547764, 29.565907, '重庆', 20],
    [103.834171, 36.06138, 106.547764, 29.565907, '兰州', 7],
    [106.23248, 38.486441, 106.547764, 29.565907, '银川', 4],
    [112.550671, 37.87059, 106.547764, 29.565907, '太原', 5],
    [118.796469, 32.058381, 106.547764, 29.565907, '南京', 20],
    [104.064759, 30.5702, 106.547764, 29.565907, '成都', 20],
    [119.296469, 26.07421, 106.547764, 29.565907, '福州', 8],
    [112.938861, 28.22778, 106.547764, 29.565907, '长沙', 12],
    [120.15515, 30.274149, 106.547764, 29.565907, '杭州', 12],
    [106.630241, 26.64702, 106.547764, 29.565907, '贵阳', 6],
    [123.432359, 41.805629, 106.547764, 29.565907, '沈阳', 3],
    [116.994931, 36.665291, 106.547764, 29.565907, '济南', 4],
    [87.616879, 43.82663, 106.547764, 29.565907, '乌鲁木齐', 3],
    [116.40717, 39.90469, 106.547764, 29.565907, '北京', 52],
    [111.75199, 40.841491, 106.547764, 29.565907, '呼和浩特', 5],
    [91.114529, 29.644141, 106.547764, 29.565907, '拉萨', 5],
    [108.366901, 22.81673, 106.547764, 29.565907, '南宁', 10],
    [114.30525, 30.59276, 106.547764, 29.565907, '武汉', 45],
    [126.535801, 45.802159, 106.547764, 29.565907, '哈尔滨', 10],
    [102.83322, 24.879659, 106.547764, 29.565907, '昆明', 15],
    [101.777819, 36.617289, 106.547764, 29.565907, '西宁', 2],
  ]

  const scatterData = []
  const lineData = []

  for (let i = 0; i < data.length; i++) {
    const item = data[i]
    const name = item[4]
    const value = item[5]

    scatterData.push({
      name,
      value: [...item.slice(0, 2), value],
    })
    lineData.push({
      name,
      value,
      coords: [item.slice(0, 2), item.slice(2, 4)],
    })
  }

  // const option: echarts.EChartsOption = {
  //   title: {
  //     text: 'World',
  //     left: 860,
  //     bottom: 540,
  //     textStyle: {
  //       color: 'rgb(254,254,254,0.7)',
  //       fontSize: 46,
  //     },
  //   },
  //   tooltip: {
  //     trigger: 'item',
  //     textStyle: {
  //       color: '#fff',
  //     },
  //     backgroundColor: 'rgba(0,0,0,0.5)',
  //     formatter: (param) => {
  //       return `${param.data.name}->重庆: ${param.data.value}`
  //     },
  //   },
  //   series: [
  //     // {
  //     //   name: 'bgLine',
  //     //   type: 'lines',
  //     //   lineStyle: {
  //     //     color: param => getColor(param.data.value),
  //     //     width: 2,
  //     //     opacity: 0.5,
  //     //     curveness: 0.2,
  //     //   },
  //     //   data: lineData,
  //     // },
  //     {
  //       name: 'scatter',
  //       type: 'effectScatter',
  //       rippleEffect: {
  //         scale: 5,
  //         brushType: 'stroke',
  //       },
  //       label: {
  //         show: true,
  //         position: 'right',
  //         formatter: '{b}',
  //       },
  //       symbolSize: 5,
  //       itemStyle: {
  //         color: param => getColor(param.data.value[2]),
  //       },
  //       data: scatterData,
  //     },
  //     {
  //       name: 'sLine',
  //       type: 'lines',
  //       effect: {
  //         show: true,
  //         period: 6,
  //         trailLength: 0.4,
  //         symbolSize: 5,
  //       },
  //       lineStyle: {
  //         color: param => getColor(param.data.value),
  //         width: 0,
  //         curveness: 0.2,
  //       },
  //       data: lineData,
  //     },
  //   ],
  // }
  const option: echarts.EChartsOption = {
    // backgroundColor: '#012248',
    title: {
      text: 'China and US',
    },
    tooltip: {
      trigger: 'item',
      showDelay: 0,
      transitionDuration: 0.2,
    },
    toolbox: {
      show: false,
      // orient: 'vertical',
      left: 'left',
      top: 'top',
      feature: {
        dataView: { readOnly: false },
        restore: {},
        saveAsImage: {},
      },
    },
    bmap: {
      // 百度地图中心经纬度。默认为 [104.114129, 37.550339]。
      center: [180.13066322374, 30.240018034923],
      // 百度地图缩放级别。默认为 5。

      layoutSize: 100,
      layoutCenter: ['30%', '30%'],
      zoom: 1,
      // 是否开启拖拽缩放，可以只设置 'scale' 或者 'move'。默认关闭。
      roam: true,
      // 百度地图的旧版自定义样式，见 https://lbsyun.baidu.com/custom/index.htm
      mapStyle: {},
      // 百度地图 3.0 之后的新版自定义样式，见 https://lbsyun.baidu.com/index.php?title=open/custom
      mapStyleV2: {
      },
      // 百度地图的初始化配置，见 https://lbsyun.baidu.com/cms/jsapi/reference/jsapi_reference.html#a0b1
      mapOptions: {
        // 禁用百度地图自带的底图可点功能
        enableMapClick: false,
        minZoom: 0.2,
      },
    },
    // geo: [
    //   {
    //     id: 0,
    //     name: 'testMap',
    //     type: 'map',
    //     map: 'testMap',
    //     projection: {
    //       project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
    //       unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
    //     },
    //     zoom: 1,
    //     emphasis: {
    //       disabled: true,
    //     },
    //     // center: [10, 30],
    //     top: 100,
    //     left: 100,
    //     regions: [{
    //       name: '粤港澳',
    //       itemStyle: {
    //         areaColor: {
    //           imageWidth: 1920,
    //           imageHeight: 1080,
    //           // image: '<img src="~/img/ygk.png">',
    //           image: mapBackground,
    //           repeat: 'no-repeat',
    //         },
    //       // color: {
    //       // },
    //       },
    //     }],
    //   }, {
    //     id: 1,
    //     name: 'sfbaMap',
    //     type: 'map',
    //     map: 'sfbaMap',
    //     projection: {
    //       project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
    //       unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
    //     },
    //     zoom: 1,
    //     emphasis: {
    //       disabled: false,
    //     },
    //     layoutCenter: [1685, 844],
    //     layoutSize: 320,
    //     // left: 1080,

    //     itemStyle: {
    //       areaColor: {
    //         imageWidth: 1920,
    //         imageHeight: 1080,
    //         image: mapBackground,

    //         // image: ygk,
    //         // repeat: 'no-repeat',
    //         repeat: 'repeat-x',
    //       },
    //     },
    //     // regions: [{
    //     //   name: 'sfba',
    //     // }],
    //   },
    // ],
    series: [
      {
        name: 'bgLine',
        coordinateSystem: 'bmap',
        type: 'lines',
        lineStyle: {
          color: param => getColor(param.data.value),
          width: 2,
          opacity: 0.5,
          curveness: 0.2,
        },
        data: lineData,
      },
      {
        name: 'scatter',
        type: 'effectScatter',
        coordinateSystem: 'bmap',
        rippleEffect: {
          scale: 5,
          brushType: 'stroke',
        },
        label: {
          show: true,
          position: 'right',
          formatter: '{b}',
        },
        symbolSize: 5,
        itemStyle: {
          color: param => getColor(param.data.value[2]),
        },
        data: scatterData,
      },
      {
        name: 'sLine',
        type: 'lines',
        coordinateSystem: 'bmap',
        effect: {
          show: true,
          period: 6,
          trailLength: 0.4,
          symbolSize: 5,
        },
        lineStyle: {
          color: param => getColor(param.data.value),
          width: 0,
          curveness: 0.2,
        },
        data: lineData,
      },
    ],
  }
  // console.log(option)
  // console.log(option)
  setTimeout(() => {
    chartObj.setOption(option)
  }, 1000)

  window.onresize = _.debounce(() => {
    chartObj.resize()
  }, 500)
})
</script>

<template>
  <div>
    <!-- <script src="" /> -->
    <div id="chartID" class="echart-container" style="width: 1920px;height:1080px" />
  </div>

  <!-- <div id="chartID" class="echart-container" style="width: 600px;height:600px;border-radius: 50%;" /> -->
</template>

<route lang="yaml">
meta:
  layout: empty
</route>

