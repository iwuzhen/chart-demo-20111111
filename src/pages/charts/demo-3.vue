<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'

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
  const response = await fetch('/data/100000_full.json')

  echarts.registerMap('china', await response.json())

  const echartsOption: echarts.EChartsOption = {
    title: {
      text: 'china',
    },
    grid: [
      {
        show: false,
        id: 0,
        right: 200,
        bottom: 200,
        // right: 1200,
        height: 300,
        width: 400,
      },
    ],
    xAxis: [
      {
        show: true,
        gridIndex: 0,
        type: 'category',
        // data: demoData.slice(1).map(item => item[0]),
      },
    ],
    yAxis: [
      {
        show: true,
        gridIndex: 0,
        max: 100,
      },
    ],
    geo: [
      {
        name: 'china',
        type: 'map',
        map: 'china',
        top: 200,
        left: 150,
        zoom: 1.5,
        roam: false,
        geoIndex: 0,
        itemStyle: {
          areaColor: '#eee',
          color: 'rgba(255,255,255,244)',
          borderColor: '#fff',
          // borderColor: '#000',
          borderWidth: 1,
        },
        center: [100, 30],
        emphasis: {
          disabled: true,
          label: {
            show: false,
          },
        },
        regions: [
        ],
      },
    ],

    animationDuration: 500,
    animationDurationUpdate: 500,
    animationDelay: 200,
    animationEasing: 'linear',
    // animationEasingUpdate: 'linear',
    series: [
      {
        name: 'China',
        // map: 'China',
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
        // hoverAnimation: true,
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
    dataset: [
      {
        id: 'dataset_raw',
        source: demoData,
      },
      ...demoData.slice(1).map((item, index, obj) => {
        const dataSetKey = `dataset_${item[0]}`
        return {
          id: dataSetKey,
          fromDatasetId: 'dataset_raw',
          transform: {
            type: 'filter',
            config: {
              and: [
                { dimension: 'year', lte: item[0] },
              ],
            },
          },
        }
      }),
    ],
    timeline: {
      show: true,
      axisType: 'category',
      autoPlay: false,
      loop: true,
      playInterval: 2000,
      data: demoData.slice(1).map(item => item[0]),
    },
    options,
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

