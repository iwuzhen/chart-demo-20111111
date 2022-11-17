<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import * as d3 from 'd3-geo'
import 'd3-array'
import { convertData } from '~/hook/utils'

const USAprojection = d3.geoAlbersUsa()

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

const dataUSA: CityData[] = [
  {
    name: '纽约',
    value: 112,
  },
]

// echarts.registerMap('USA', chinaGeo, {
//   Alaska: {
//     // 把阿拉斯加移到美国主大陆左下方
//     left: -131,
//     top: 25,
//     width: 15
//   },
//   Hawaii: {
//     left: -110,
//     top: 28,
//     width: 5
//   },
//   'Puerto Rico': {
//     // 波多黎各
//     left: -76,
//     top: 26,
//     width: 2
//   }
// });

onMounted(async () => {
  const elem = document.getElementById('chartID')
  // let chartObj = echarts.init(elem);
  if (!elem)
    return

  const chartObj = echarts.init(elem, undefined, { renderer: 'svg' })

  let response = await fetch('/data/usa.json')
  echarts.registerMap('USA', await response.json())

  response = await fetch('/data/100000_full.json')
  echarts.registerMap('china', await response.json())

  const option = {
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
        name: 'china',
        type: 'map',
        map: 'china',
        left: 20,
        zoom: 1,
        roam: false,
        geoIndex: 0,
        itemStyle: {
          areaColor: '#eee',
          color: 'rgba(255,255,255,244)',
          borderColor: '#fff',
        },
        center: [100, 30],
        emphasis: {
          disabled: true,
          label: {
            show: false,
          },
        },
      },
      {
        geoIndex: 1,
        name: 'USA',
        type: 'map',
        map: 'USA',
        right: 40,
        projection: {
          project(point: [number, number]) {
            return USAprojection(point)
          },
          unproject(point: [number, number]) {
            if (USAprojection.invert)
              return USAprojection.invert(point)
          },
        },
        center: ['0%', '40%'],
        // layoutCenter: ['30%', '30%'],
        // layoutSize: 500,
        itemStyle: {
          areaColor: '#eee',
          color: 'rgba(255,255,255,244)',
          borderColor: '#fff',
        },
        emphasis: {
          label: {
            show: true,
          },
        },
      },
    ],
    series: [
      {
        name: 'china',
        map: 'china',
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
      {
        name: 'USA',
        type: 'effectScatter',
        coordinateSystem: 'geo',
        geoIndex: 1,
        data: convertData(
          dataUSA,
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
  // console.log(option)
  chartObj.setOption(option)

  window.onresize = _.debounce(() => {
    chartObj.resize()
  }, 500)
})
</script>

<template>
  <div class="echart-container" id="chartID" style="width: 1920px;height:1080px" />
</template>

<route lang="yaml">
meta:
  layout: empty
</route>

