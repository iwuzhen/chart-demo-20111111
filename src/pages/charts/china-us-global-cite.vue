<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import 'd3-array'
// import { convertData, scale } from '~/hook/utils'

onMounted(async () => {
  const response = await fetch('/data/world_r.json')
  echarts.registerMap('world', await response.json())

  const elem = document.getElementById('chartID')
  // let chartObj = echarts.init(elem);
  if (!elem)
    return

  const chartObj = echarts.init(elem, undefined, { renderer: 'canvas' })
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
    [113.26388, 23.12946, 256.547764, 29.565907, '广州', 60],
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
    geo: [
      {
        id: 0,
        name: 'world',
        type: 'map',
        map: 'world',
        projection: {
          project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
          unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
        },
        zoom: 1.4,
        emphasis: {
          disabled: true,
        },
        // center: [10, 30],
        top: -30,
        left: 300,
      },
    ],
    series: [
      {
        name: 'bgLine',
        coordinateSystem: 'geo',
        type: 'lines',
        lineStyle: {
          color: param => getColor(param.data.value),
          width: 10,
          opacity: 0.5,
          curveness: 0.2,
        },
        clip: false,
        data: lineData,
      },
      {
        name: 'scatter',
        type: 'effectScatter',
        coordinateSystem: 'geo',
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
        coordinateSystem: 'geo',
        effect: {
          show: true,
          period: 6,
          trailLength: 0.4,
          symbolSize: 20,
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

