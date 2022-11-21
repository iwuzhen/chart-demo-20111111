<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import 'd3-array'
// import { convertData, scale } from '~/hook/utils'
import ygk from '~/assert/ygk.png'
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
    // bmap: {
    //   // 百度地图中心经纬度。默认为 [104.114129, 37.550339]。
    //   center: [120.13066322374, 30.240018034923],
    //   // 百度地图缩放级别。默认为 5。
    //   outerWidth: 300,
    //   innerHeight: 300,
    //   width: 300,
    //   height: 300,

    //   layoutSize: 100,
    //   layoutCenter: ['30%', '30%'],
    //   zoom: 14,
    //   // 是否开启拖拽缩放，可以只设置 'scale' 或者 'move'。默认关闭。
    //   roam: false,
    //   // 百度地图的旧版自定义样式，见 https://lbsyun.baidu.com/custom/index.htm
    //   mapStyle: {},
    //   // 百度地图 3.0 之后的新版自定义样式，见 https://lbsyun.baidu.com/index.php?title=open/custom
    //   mapStyleV2: {
    //   },
    //   // 百度地图的初始化配置，见 https://lbsyun.baidu.com/cms/jsapi/reference/jsapi_reference.html#a0b1
    //   mapOptions: {
    //     // 禁用百度地图自带的底图可点功能
    //     enableMapClick: false,
    //     size: {
    //       width: 300,
    //       height: 300,
    //     },
    //   },
    // },
    geo: [
      {
        id: 0,
        name: 'testMap',
        type: 'map',
        map: 'testMap',
        projection: {
          project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
          unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
        },
        zoom: 1,
        emphasis: {
          disabled: true,
        },
        // center: [10, 30],
        top: 100,
        left: 100,
        regions: [{
          name: '粤港澳',
          itemStyle: {
            areaColor: {
              imageWidth: 1920,
              imageHeight: 1080,
              // image: '<img src="~/img/ygk.png">',
              image: mapBackground,
              repeat: 'no-repeat',
            },
          // color: {
          // },
          },
        }],
      }, {
        id: 1,
        name: 'sfbaMap',
        type: 'map',
        map: 'sfbaMap',
        projection: {
          project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
          unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
        },
        zoom: 1,
        emphasis: {
          disabled: false,
        },
        layoutCenter: [1685, 844],
        layoutSize: 320,
        // top: 640,
        // left: 1480,
        // center: [10, 30],
        // bottom: '80px',
        // right: '77px',
        // right: 0,
        // bottom: 0,
        // top: 440,
        // left: 1080,

        itemStyle: {
          areaColor: {
            imageWidth: 1920,
            imageHeight: 1080,
            image: mapBackground,

            // image: ygk,
            // repeat: 'no-repeat',
            repeat: 'repeat-x',
          },
        },
        // regions: [{
        //   name: 'sfba',
        // }],
      },
    ],
    series: [
    ],
  }
  // console.log(option)
  // console.log(option)
  chartObj.setOption(option)

  window.onresize = _.debounce(() => {
    chartObj.resize()
  }, 500)
})
</script>

<template>
  <div id="chartID" class="echart-container" style="width: 1920px;height:1080px" />
  <!-- <div id="chartID" class="echart-container" style="width: 600px;height:600px;border-radius: 50%;" /> -->
</template>

<route lang="yaml">
meta:
  layout: empty
</route>

