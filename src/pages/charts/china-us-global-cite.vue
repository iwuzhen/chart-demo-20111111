<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import 'd3-array'
import { geoCoordMap, scale } from '~/hook/utils'

const sourceData = [
  [1987, 'JP', '天津', 1],
  [2020, 'US', '天津', 2650],
  [2020, 'IN', '天津', 1143],
  [2020, 'GB', '天津', 955],
  [2020, 'AU', '天津', 677],
  [2020, 'KR', '天津', 600],
  [2020, 'CA', '天津', 550],
  [2020, 'JP', '天津', 444],
  [2020, 'IR', '天津', 413],
  [2020, 'SG', '天津', 410],
  [2020, 'DE', '天津', 338],
  [2020, 'US', '广州', 4843],
  [2020, 'IN', '广州', 2268],
  [2020, 'GB', '广州', 1883],
  [2020, 'AU', '广州', 1476],
  [2020, 'CA', '广州', 1079],
  [2020, 'KR', '广州', 998],
  [2020, 'IR', '广州', 767],
  [2020, 'DE', '广州', 755],
  [2020, 'JP', '广州', 680],
  [2020, 'SG', '广州', 662],
  [2020, 'US', '上海', 9860],
  [2020, 'IN', '上海', 3426],
  [2020, 'GB', '上海', 2947],
  [2020, 'AU', '上海', 2084],
  [2020, 'CA', '上海', 1915],
  [2020, 'KR', '上海', 1665],
  [2020, 'DE', '上海', 1513],
  [2020, 'JP', '上海', 1252],
  [2020, 'IR', '上海', 1183],
  [2020, 'SG', '上海', 1072],
  [2020, 'US', '长沙', 3027],
  [2020, 'IN', '长沙', 1749],
  [2020, 'GB', '长沙', 1177],
  [2020, 'CA', '长沙', 965],
  [2020, 'AU', '长沙', 812],
  [2020, 'IR', '长沙', 737],
  [2020, 'KR', '长沙', 687],
  [2020, 'DE', '长沙', 531],
  [2020, 'ES', '长沙', 450],
  [2020, 'JP', '长沙', 415],
  [2020, 'CN', '纽约', 31094],
  [2020, 'GB', '纽约', 12288],
  [2020, 'DE', '纽约', 9746],
  [2020, 'CA', '纽约', 6851],
  [2020, 'IN', '纽约', 6105],
  [2020, 'AU', '纽约', 5544],
  [2020, 'FR', '纽约', 4706],
  [2020, 'IT', '纽约', 4314],
  [2020, 'JP', '纽约', 4024],
  [2020, 'KR', '纽约', 3753],
  [2020, 'US', '纽约', 5140],
  [2020, 'GB', '纽约', 1632],
  [2020, 'IN', '纽约', 1562],
  [2020, 'AU', '纽约', 1359],
  [2020, 'CA', '纽约', 1220],
  [2020, 'DE', '纽约', 853],
  [2020, 'KR', '纽约', 838],
  [2020, 'SG', '纽约', 758],
  [2020, 'JP', '纽约', 624],
  [2020, 'IR', '纽约', 603],
  [2020, 'US', '深圳', 5541],
  [2020, 'GB', '深圳', 1706],
  [2020, 'IN', '深圳', 1637],
  [2020, 'AU', '深圳', 1229],
  [2020, 'CA', '深圳', 1152],
  [2020, 'KR', '深圳', 954],
  [2020, 'DE', '深圳', 893],
  [2020, 'SG', '深圳', 831],
  [2020, 'JP', '深圳', 573],
  [2020, 'FR', '深圳', 544],
  [2020, 'US', '北京', 32008],
  [2020, 'IN', '北京', 9672],
  [2020, 'GB', '北京', 9495],
  [2020, 'AU', '北京', 6718],
  [2020, 'CA', '北京', 6233],
  [2020, 'DE', '北京', 5312],
  [2020, 'KR', '北京', 5065],
  [2020, 'SG', '北京', 3720],
  [2020, 'JP', '北京', 3560],
  [2020, 'IR', '北京', 3508],
  [2020, 'US', '南京', 6917],
  [2020, 'IN', '南京', 3637],
  [2020, 'GB', '南京', 2495],
  [2020, 'AU', '南京', 1964],
  [2020, 'CA', '南京', 1737],
  [2020, 'IR', '南京', 1533],
  [2020, 'KR', '南京', 1405],
  [2020, 'ES', '南京', 969],
  [2020, 'JP', '南京', 948],
  [2020, 'SG', '南京', 894],
  [2020, 'US', '武汉', 5370],
  [2020, 'IN', '武汉', 2786],
  [2020, 'GB', '武汉', 2127],
  [2020, 'AU', '武汉', 1650],
  [2020, 'CA', '武汉', 1343],
  [2020, 'IR', '武汉', 1221],
  [2020, 'KR', '武汉', 1098],
  [2020, 'DE', '武汉', 862],
  [2020, 'SA', '武汉', 860],
  [2020, 'JP', '武汉', 825],
  [2020, 'US', '成都', 4502],
  [2020, 'IN', '成都', 2207],
  [2020, 'GB', '成都', 1472],
  [2020, 'AU', '成都', 1300],
  [2020, 'CA', '成都', 1227],
  [2020, 'KR', '成都', 949],
  [2020, 'IR', '成都', 890],
  [2020, 'ES', '成都', 867],
  [2020, 'PK', '成都', 747],
  [2020, 'SG', '成都', 732],
]

const getMMcolor = (obj) => {
  // console.log(object)
  if (obj.data.dest === '纽约')
    return 'blue'
  return 'red'
}

const getSize = (value) => {
  // console.log(object)
  return scale(value, 0, 31094, 2, 50)
}

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
    if (value <= 4000)
      return colors[0]

    else if (value > 4000 && value <= 8000)
      return colors[1]

    else if (value > 8000 && value <= 16000)
      return colors[2]

    else
      return colors[3]
  }

  // const data = [
  //   [113.26388, 23.12946, 256.547764, 29.565907, '广州', 60],
  //   [108.939839, 34.34127, 106.547764, 29.565907, '西安', 5],
  //   [113.624931, 34.74725, 106.547764, 29.565907, '郑州', 12],
  //   [115.857941, 28.68202, 106.547764, 29.565907, '南昌', 10],
  //   [110.199889, 20.044221, 106.547764, 29.565907, '海口', 5],
  //   [121.47375, 31.23026, 106.547764, 29.565907, '上海', 62],
  //   [125.32357, 43.81602, 106.547764, 29.565907, '长春', 18],
  //   [117.22901, 31.820571, 106.547764, 29.565907, '合肥', 18],
  //   [114.514299, 38.04276, 106.547764, 29.565907, '石家庄', 18],
  //   [117.199371, 39.0851, 106.547764, 29.565907, '天津', 18],
  //   [106.550729, 29.564709, 106.547764, 29.565907, '重庆', 20],
  //   [103.834171, 36.06138, 106.547764, 29.565907, '兰州', 7],
  //   [106.23248, 38.486441, 106.547764, 29.565907, '银川', 4],
  //   [112.550671, 37.87059, 106.547764, 29.565907, '太原', 5],
  //   [118.796469, 32.058381, 106.547764, 29.565907, '南京', 20],
  //   [104.064759, 30.5702, 106.547764, 29.565907, '成都', 20],
  //   [119.296469, 26.07421, 106.547764, 29.565907, '福州', 8],
  //   [112.938861, 28.22778, 106.547764, 29.565907, '长沙', 12],
  //   [120.15515, 30.274149, 106.547764, 29.565907, '杭州', 12],
  //   [106.630241, 26.64702, 106.547764, 29.565907, '贵阳', 6],
  //   [123.432359, 41.805629, 106.547764, 29.565907, '沈阳', 3],
  //   [116.994931, 36.665291, 106.547764, 29.565907, '济南', 4],
  //   [87.616879, 43.82663, 106.547764, 29.565907, '乌鲁木齐', 3],
  //   [116.40717, 39.90469, 106.547764, 29.565907, '北京', 52],
  //   [111.75199, 40.841491, 106.547764, 29.565907, '呼和浩特', 5],
  //   [91.114529, 29.644141, 106.547764, 29.565907, '拉萨', 5],
  //   [108.366901, 22.81673, 106.547764, 29.565907, '南宁', 10],
  //   [114.30525, 30.59276, 106.547764, 29.565907, '武汉', 45],
  //   [126.535801, 45.802159, 106.547764, 29.565907, '哈尔滨', 10],
  //   [102.83322, 24.879659, 106.547764, 29.565907, '昆明', 15],
  //   [101.777819, 36.617289, 106.547764, 29.565907, '西宁', 2],
  // ]

  const scatterData = []
  const lineData = []

  for (let i = 0; i < sourceData.length; i++) {
    const item = sourceData[i]
    const name = item[1]
    const value = item[3]

    scatterData.push({
      name,
      value: [...item.slice(0, 2), value],
    })
    const coordinateA = geoCoordMap[item[1]]
    const coordinateB = geoCoordMap[item[2]]
    if (!coordinateA || !coordinateB)
      continue
    if (coordinateA[0] < -10)
      coordinateA[0] += 360
    if (coordinateB[0] < -10)
      coordinateB[0] += 360

    lineData.push({
      name,
      value,
      source: item[1],
      dest: item[2],
      coords: [coordinateB, coordinateA],
    })
  }

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
      // {
      //   name: 'bgLine',
      //   coordinateSystem: 'geo',
      //   type: 'lines',
      //   lineStyle: {
      //     color: param => getMMcolor(param),
      //     // width: param => getSize(param),
      //     width: 2,
      //     opacity: 0.5,
      //     curveness: 0.2,
      //   },
      //   clip: false,
      //   data: lineData,
      // },
      ...lineData.map((obj) => {
        // console.log(obj)
        return {
          name: 'bgLine',
          coordinateSystem: 'geo',
          type: 'lines',
          lineStyle: {
            color: param => getMMcolor(param),
            width: getSize(obj.value),
            // width: 2,
            opacity: 0.5,
            curveness: 0.2,
          },
          clip: false,
          data: [obj],
        }
      }),
      // {
      //   name: 'scatter',
      //   type: 'effectScatter',
      //   coordinateSystem: 'geo',
      //   rippleEffect: {
      //     scale: 5,
      //     brushType: 'stroke',
      //   },
      //   label: {
      //     show: true,
      //     position: 'right',
      //     formatter: '{b}',
      //   },
      //   symbolSize: 5,
      //   itemStyle: {
      //     color: param => getColor(param.data.value[2]),
      //   },
      //   data: scatterData,
      // },
      // {
      //   name: 'sLine',
      //   type: 'lines',
      //   coordinateSystem: 'geo',
      //   effect: {
      //     show: true,
      //     period: 6,
      //     trailLength: 0.6,
      //     symbolSize: [10, 20],
      //   },
      //   lineStyle: {
      //     color: param => getMMcolor(param),
      //     width: param => getSize(param),
      //     curveness: 0.2,
      //   },
      //   data: lineData,
      // },
    ],
    animationDuration: 10000,
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

