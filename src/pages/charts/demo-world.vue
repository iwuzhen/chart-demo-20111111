<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import * as d3 from 'd3-geo-projection'

// import 'd3-array'
import { convertData } from '~/hook/utils'

const demoData = [
  ['year', '北京', '上海', '广州', '深圳', '重庆'],
  [2000, 12, 17, 23, 12, 24],
  [2001, 14, 18, 28, 17, 25],
  [2002, 16, 19, 33, 22, 26],
  [2003, 18, 20, 38, 27, 27],
  [2004, 20, 21, 43, 32, 28],
  [2005, 22, 22, 48, 37, 29],
  [2006, 24, 23, 53, 42, 30],
  [2007, 26, 24, 58, 47, 31],
  [2008, 28, 25, 63, 52, 32],
  [2009, 30, 26, 68, 57, 33],
  [2010, 32, 27, 73, 62, 34],
  [2011, 34, 28, 78, 67, 35],
  [2012, 36, 29, 83, 72, 36],
  [2013, 38, 30, 88, 77, 37],
  [2014, 40, 31, 93, 82, 38],
  [2015, 42, 32, 98, 87, 39],
  [2016, 44, 33, 103, 92, 40],
  [2017, 46, 34, 108, 97, 41],
  [2018, 48, 35, 113, 102, 42],
  [2019, 50, 36, 118, 107, 43],
]

const dataChina: CityData[] = [
  {
    name: '肇庆',
    value: 12,
  },
  {
    name: '香港',
    value: 23,
  },
  {
    name: '澳门',
    value: 30,
  },
  {
    name: '中山',
    value: 40,
  },
  {
    name: '东莞',
    value: 52,
  },
  {
    name: '惠州',
    value: 62,
  },
  {
    name: '佛山',
    value: 72,
  },
  {
    name: '广州',
    value: 72,
  },
  {
    name: '江门',
    value: 72,
  },
  {
    name: '珠海',
    value: 72,
  },
  {
    name: '深圳',
    value: 72,
  },
  {
    name: '上海',
    value: 72,
  },
]
const processLng = (lng) => {
  return lng > -30 ? lng - 150 : lng + 210
}

onMounted(async () => {
  const elem = document.getElementById('chartID')
  // let chartObj = echarts.init(elem);
  if (!elem)
    return

  const options: echarts.EChartsOption[] = demoData.slice(1).map((item, index, obj) => {
    return {
      series: [
        ...demoData[0].slice(1).map((cityName) => {
          return {
            type: 'line',
            xAxisIndex: 0,
            yAxisIndex: 0,
            showSymbol: false,
            datasetId: `dataset_${item[0]}`,
            encode: {
              x: 'year', // 表示维度 3、1、5 映射到 x 轴。
              y: cityName, // 表示维度 2 映射到 y 轴。
            },
          }
        },
        ),
        {
          name: 'China',
          map: 'China',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          geoIndex: 0,
          data: convertData(
            dataChina,
          ),
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            return val[2] / 10
          },
          showEffectOn: 'emphasis',
          rippleEffect: {
            brushType: 'stroke',
          },
          hoverAnimation: true,
          label: {
            formatter: '{b}',
            position: 'right',
            show: false,
          },
          itemStyle: {
            color: '#f4e925',
            shadowBlur: 10,
            shadowColor: '#333',
          },
        },
      ],
    }
  })
  // const chartObj = echarts.init(elem, undefined, { renderer: 'svg' })
  const chartObj = echarts.init(elem)
  const response = await fetch('/data/world.geo.json')
  const coordinatesList = []

  const worldMap = await response.json()
  worldMap.features.map((district) => {
    if (district.geometry.type == 'Polygon') {
      district.geometry.coordinates.map((border) => {
        border.map((coord) => {
          coord[0] = processLng(coord[0])

          coordinatesList.push(coord)
        })
      })
    }
    else {
      district.geometry.coordinates.map((border) => {
        border.map((coordinates) => {
          coordinates.map((coord) => {
            coord[0] = processLng(coord[0])

            coordinatesList.push(coord)
          })
        })
      })
    }
  })
  echarts.registerMap('world', worldMap)
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

  const echartsOption: echarts.EChartsOption = {
    title: {
      text: 'china',
    },
    geo: [
      {
        name: 'china',
        type: 'map',
        map: 'world',
        top: 200,
        left: 150,
        zoom: 1,
        roam: true,
        projection: {
          // project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
          // unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
        },
        itemStyle: {
          areaColor: '#eee',
          color: 'rgba(255,255,255,244)',
          borderColor: '#fff',
          // borderColor: '#000',
          borderWidth: 1,
        },
        center: [100, 30],
        emphasis: {
          disabled: false,
          label: {
            show: false,
          },
        },
        regions: [
        ],
      },
    ],
    series: [
      {
        name: 'bgLine',
        coordinateSystem: 'geo',
        type: 'lines',
        lineStyle: {
          color: param => getColor(param.data.value),
          width: 2,
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
    animationDuration: 500,
    animationDurationUpdate: 500,
    animationDelay: 200,
    animationEasing: 'linear',
    // animationEasingUpdate: 'linear',
  }

  // eslint-disable-next-line no-console
  console.log(echartsOption)
  chartObj.setOption(echartsOption)

  window.onresize = _.debounce(() => {
    chartObj.resize()
  }, 500)
})
</script>

<template>
  <div id="chartID" class="echart-container" style="width: 1920px;height:1080px" />
</template>

<route lang="yaml">
meta:
  layout: empty
</route>

