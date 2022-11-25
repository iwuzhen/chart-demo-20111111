<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import * as d3 from 'd3-geo'
import 'd3-array'
import { convertData, scale } from '~/hook/utils'

const USAprojection = d3.geoAlbersUsa()

const demoData = [
  ['year', '纽约', '旧金山湾区', '上海', '粤港澳湾区'],
  [1980, 1732, 1311, 0, 19],
  [1981, 1901, 1414, 2, 23],
  [1982, 2089, 1566, 2, 27],
  [1983, 2279, 1742, 6, 38],
  [1984, 2463, 1937, 10, 45],
  [1985, 2706, 2112, 14, 57],
  [1986, 2964, 2342, 18, 67],
  [1987, 3268, 2614, 28, 73],
  [1988, 3682, 3004, 50, 93],
  [1989, 4159, 3484, 65, 121],
  [1990, 4655, 3992, 81, 155],
  [1991, 5215, 4514, 105, 204],
  [1992, 5825, 5179, 138, 255],
  [1993, 6417, 5873, 173, 338],
  [1994, 7039, 6682, 203, 514],
  [1995, 7674, 7364, 231, 714],
  [1996, 8321, 8132, 289, 1013],
  [1997, 9019, 9034, 343, 1337],
  [1998, 9680, 9939, 419, 1742],
  [1999, 10339, 10959, 502, 2137],
  [2000, 11121, 11965, 657, 2570],
  [2001, 11899, 12985, 843, 3124],
  [2002, 12859, 14085, 1132, 3768],
  [2003, 13934, 15417, 1516, 4513],
  [2004, 15185, 16880, 2217, 5532],
  [2005, 16455, 18548, 3185, 6758],
  [2006, 17880, 20428, 4453, 8378],
  [2007, 19393, 22355, 5932, 10164],
  [2008, 21030, 24593, 7928, 12381],
  [2009, 22801, 27060, 10222, 14876],
  [2010, 24856, 29856, 12675, 17718],
  [2011, 26965, 32569, 14983, 20464],
  [2012, 29303, 35695, 17194, 23235],
  [2013, 31776, 39074, 19415, 25988],
  [2014, 34433, 42746, 21685, 29069],
  [2015, 37344, 46858, 23861, 32105],
  [2016, 40557, 51353, 26089, 35653],
  [2017, 44114, 56551, 28719, 40079],
  [2018, 48523, 63050, 32185, 45893],
  [2019, 53907, 71876, 37128, 53931],
  [2020, 59808, 81200, 42999, 63676],
  [2021, 66193, 91184, 51185, 77463],
  [2022, 68535, 94659, 55465, 83884],
]

const dataChina: CityData[] = [
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
  const elem = document.getElementById('chartID')
  // let chartObj = echarts.init(elem);
  if (!elem)
    return

  const options: echarts.EChartsOption[] = demoData.slice(1).map((item, index, obj): echarts.EChartsOption => {
    return {
      title: {
        text: `China and USA ${item[0]}`,
      },
      series: [
        ...demoData[0].slice(1).map((cityName, cityIndex): echarts.SeriesOption => {
          return {
            id: cityName,
            type: 'line',
            xAxisIndex: 0,
            yAxisIndex: 0,
            showSymbol: false,
            endLabel: {
              show: true,
              fontSize: 20,
              formatter(params: any) {
                // console.log(params)
                return `${params.seriesId}: ${params.value[1]}`
              },
            },
            data: demoData.slice(1).map((dataItem, currentIndex) => {
              if (currentIndex <= index)
                return [dataItem[0], dataItem[cityIndex + 1]]
              else
                return [dataItem[0], '-']
            }),
          }
        }),
        {
          name: 'China',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          effectType: 'ripple',
          rippleEffect: {
            brushType: 'fill',
          },
          geoIndex: 0,
          data: convertData(
            demoData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: demoData[index + 1][cityIndex + 1] as number,
              }
            }).filter(arrayItem => ['上海', '粤港澳湾区'].includes(arrayItem.name)),
          ),
          labelLine: {
            show: true,
            length2: 5,
            lineStyle: {
              color: '#bbb',
            },
          },
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            // 线性映射到 0-100的范围
            return scale(val[2], 0, 94659, 5, 60)
          },
          showEffectOn: 'render',
          label: {
            formatter: '{b}',
            fontSize: 20,
            color: '#fff',
            position: 'right',
            show: true,
          },
          itemStyle: {
            color: '#f4e925',
            shadowBlur: 10,
            shadowColor: '#333',
          },
        }, {
          name: 'USA',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          effectType: 'ripple',
          geoIndex: 1,
          data: convertData(
            demoData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: demoData[index + 1][cityIndex + 1] as number,
              }
            }).filter(arrayItem => ['纽约', '旧金山湾区'].includes(arrayItem.name)),
          ),
          labelLine: {
            show: true,
            length2: 5,
            lineStyle: {
              color: '#bbb',
            },
          },
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            // 线性映射到 0-100的范围
            return scale(val[2], 0, 94659, 5, 60)
          },
          showEffectOn: 'render',
          rippleEffect: {
            brushType: 'stroke',
          },
          label: {
            formatter: '{b}',
            fontSize: 20,
            color: '#fff',
            position: 'left',
            show: true,
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

  const chartObj = echarts.init(elem, undefined, { renderer: 'svg' })

  let response = await fetch('/data/usa.json')
  echarts.registerMap('USA', await response.json())

  response = await fetch('/data/100000_full.json')
  echarts.registerMap('China', await response.json())

  const option: echarts.EChartsOption = {
    backgroundColor: '#012248',
    title: {
      text: 'China and US',
    },
    grid: [
      {
        show: false,
        id: 0,
        right: 700,
        bottom: 100,
        // right: 1200,
        height: 200,
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
        max: 100000,
      },
    ],
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
        id: 0,
        type: 'map',
        map: 'China',
        left: 20,
        zoom: 1,
        roam: false,
        itemStyle: {
          areaColor: '#eee',
          color: 'rgba(255,255,255,244)',
          borderColor: '#fff',
        },
        center: [100, 30],
        // center: ['0%', '10%'],
        // emphasis: {
        //   disabled: true,
        //   label: {
        //     show: false,
        //   },
        // },
        // itemStyle: {

        //     areaColor: '#24CFF4',
        //     borderColor: '#53D9FF',
        //     borderWidth: 1.3,
        //     shadowBlur: 15,
        //     shadowColor: '#3a73c0',
        //     shadowOffsetX: 7,
        //     shadowOffsetY: 6,
        //   },
        //   emphasis: {
        //     areaColor: '#8dd7fc',
        //     borderWidth: 1.6,
        //     shadowBlur: 25,
        //   },
        // },
      },
      {
        id: 1,
        name: 'USA',
        type: 'map',
        map: 'USA',
        right: 40,
        projection: {
          project(point: [number, number]) {
            return USAprojection(point) as number[]
          },
          unproject(point: [number, number]) {
            if (USAprojection.invert)
              return USAprojection.invert(point) as number[]
            return []
          },
        },
        center: ['0%', '30%'],
        // layoutCenter: ['30%', '30%'],
        // layoutSize: 500,
        // itemStyle: {
        //   areaColor: '#eee',
        //   color: 'rgba(255,255,255,244)',
        //   borderColor: '#fff',
        // },
        itemStyle: {

          areaColor: '#24CFF4',
          borderColor: '#53D9FF',
          borderWidth: 1.3,
          shadowBlur: 15,
          shadowColor: '#3a73c0',
          shadowOffsetX: 7,
          shadowOffsetY: 6,

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
    timeline: {
      show: true,
      axisType: 'category',
      autoPlay: true,
      loop: false,
      playInterval: 500,
      data: demoData.slice(1).map(item => item[0]),
    },
    options,
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
</template>

<route lang="yaml">
meta:
  layout: empty
</route>

