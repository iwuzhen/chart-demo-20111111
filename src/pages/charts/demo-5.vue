<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import * as d3 from 'd3-geo'
import 'd3-array'
import { convertData, scale } from '~/hook/utils'
import mapBackground from '~/assert/mapBackground.png'
import { geoMapItemStyle } from '~/hook/sharedata'

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

onMounted(async () => {
  let response = await fetch('/data/usa.json')
  echarts.registerMap('USA', await response.json())

  response = await fetch('/data/100000_full.json')
  echarts.registerMap('China', await response.json())

  response = await fetch('/data/gba.json')
  echarts.registerMap('gbaMap', await response.json())

  response = await fetch('/data/sfba.json')
  echarts.registerMap('sfbaMap', await response.json())

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
          id: 'China',
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
            }).filter(arrayItem => ['上海'].includes(arrayItem.name)),
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
          id: 'USA',
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
        {
          id: 'ChinaS',
          name: 'ChinaS',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          effectType: 'ripple',
          rippleEffect: {
            brushType: 'fill',
          },
          geoIndex: 2,
          data: convertData(
            demoData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: demoData[index + 1][cityIndex + 1] as number,
              }
            }).filter(arrayItem => ['粤港澳湾区'].includes(arrayItem.name)),
          ),
          labelLine: {
            show: true,
            length2: 5,
            lineStyle: {
              color: '#bbb',
            },
          },
          symbol: 'pin',
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
            color: 'red',
            position: 'right',
            show: true,
          },
          itemStyle: {
            color: '#f4e925',
            shadowBlur: 10,
            shadowColor: '#333',
          },
        }, {
          id: 'USAS',
          name: 'USAS',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          effectType: 'ripple',
          geoIndex: 3,
          symbol: 'pin',
          data: convertData(
            demoData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: demoData[index + 1][cityIndex + 1] as number,
              }
            }).filter(arrayItem => ['旧金山湾区'].includes(arrayItem.name)),
          ),
          labelLine: {
            show: true,
            length2: 5,
            lineStyle: {
              color: 'red',
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
            color: 'red',
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
        zoom: 1.1,
        roam: false,
        emphasis: {
          disabled: true,
        },
        // itemStyle: {
        //   areaColor: '#eee',
        //   color: 'rgba(255,255,255,244)',
        //   borderColor: '#fff',
        // },
        center: [100, 30],
        // center: ['0%', '10%'],
        // emphasis: {
        //   disabled: true,
        //   label: {
        //     show: false,
        //   },
        // },
        itemStyle: geoMapItemStyle.style_1,
      },
      {
        id: 1,
        zoom: 0.95,
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
        emphasis: {
          disabled: true,
        },
        center: ['0%', '33%'],
        // layoutCenter: ['30%', '30%'],
        // layoutSize: 500,
        // itemStyle: {
        //   areaColor: '#eee',
        //   color: 'rgba(255,255,255,244)',
        //   borderColor: '#fff',
        // },
        itemStyle: geoMapItemStyle.style_1,
      }, {
        id: 2,
        name: 'gbaMap',
        type: 'map',
        map: 'gbaMap',
        zoom: 1,
        layoutCenter: [207, 846],
        layoutSize: 338,
        emphasis: {
          disabled: false,
        },
        projection: {
          project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
          unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
        },
        itemStyle: {
          areaColor: {
            imageWidth: 1920,
            imageHeight: 1080,
            image: mapBackground,

            // image: ygk,
            repeat: 'no-repeat',
            // repeat: 'repeat-x',
          },
        },
        // right: 40,
        // center: ['0%', '30%'],
        // layoutCenter: ['30%', '30%'],
        // layoutSize: 500,
        // itemStyle: {
        //   areaColor: '#eee',
        //   color: 'rgba(255,255,255,244)',
        //   borderColor: '#fff',
        // },
        // itemStyle: {
        //   normal: {
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
        id: 3,
        name: 'sfbaMap',
        type: 'map',
        map: 'sfbaMap',
        projection: {
          project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
          unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
        },
        zoom: 1,
        emphasis: {
          disabled: true,
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
            repeat: 'no-repeat',
            // repeat: 'repeat-x',
          },
        },
        // regions: [{
        //   name: 'sfba',
        // }],
      },
    ],
    series: [
      // {
      //   name: 'china',
      //   type: 'effectScatter',
      //   coordinateSystem: 'geo',
      //   geoIndex: 0,
      //   data: convertData(
      //     dataChina,
      //   ),
      //   encode: {
      //     value: 2,
      //   },
      //   symbolSize(val: number[]) {
      //     return val[2] / 10
      //   },
      //   showEffectOn: 'emphasis',
      //   rippleEffect: {
      //     brushType: 'stroke',
      //   },
      //   // hoverAnimation: true,
      //   label: {
      //     formatter: '{b}',
      //     position: 'right',
      //     show: false,
      //   },
      //   itemStyle: {
      //     color: '#f4e925',
      //     shadowBlur: 10,
      //     shadowColor: '#333',
      //   },
      // },
      // {
      //   name: 'USA',
      //   type: 'effectScatter',
      //   coordinateSystem: 'geo',
      //   geoIndex: 1,
      //   data: convertData(
      //     dataUSA,
      //   ),
      //   encode: {
      //     value: 2,
      //   },
      //   symbolSize(val: number[]) {
      //     return val[2] / 10
      //   },
      //   showEffectOn: 'emphasis',
      //   rippleEffect: {
      //     brushType: 'stroke',
      //   },
      //   // hoverAnimation: true,
      //   label: {
      //     formatter: '{b}',
      //     position: 'right',
      //     show: false,
      //   },
      //   itemStyle: {
      //     color: '#f4e925',
      //     shadowBlur: 10,
      //     shadowColor: '#333',
      //   },
      // },
      // {
      //   type: 'custom',
      //   coordinateSystem: 'geo',
      //   geoIndex: 0,
      //   renderItem(params, api) {
      //     const point = api.coord(geoCoordMap['粤港澳湾区'])
      //     return {
      //       type: 'group',
      //       // left: 'center',
      //       // top: 'center',
      //       children: [{
      //         type: 'circle',
      //         shape: {
      //           cx: point[0],
      //           cy: point[1],
      //           r: 30,
      //         },
      //         itemStyle: {
      //           opacity: 0.1,
      //         },
      //       }],
      //     }
      //   },
      //   itemStyle: {
      //     opacity: 0.1,
      //   },
      //   animation: false,
      //   silent: true,
      //   data: [0],
      //   z: 10,
      // },
      {
        id: 'ChinaPoint',
        type: 'scatter',
        coordinateSystem: 'geo',
        geoIndex: 0,
        data: [[113.541389, 22.195833]],

        labelLine: {
          show: true,
          length2: 20,
          lineStyle: {
            color: '#bbb',
          },
        },
        symbol: 'circle',
        symbolSize: 80,
        label: {
          formatter: '{b}',
          fontSize: 20,
          color: 'red',
          position: 'right',
          show: true,
        },
        itemStyle: {
          color: 'rgba(255, 255, 255, 0)',
          borderWidth: 4,
          borderColor: 'rgba(238, 3, 3, 1)',
          borderType: 'dashed',
          shadowBlur: 0,
          opacity: 0.6,
          shadowColor: '#333',
        },
      },
    ],
    graphic: [
      {
        type: 'group',
        children: [
          {
            type: 'line',
            shape: {
              x1: 533,
              y1: 697,
              x2: 368,
              y2: 769,
            },
            style: {
              lineWidth: 10,
              stroke: 'red',
            },
            z: 30,
          },
        ],
        // z: 3000,
      },
    ],
    timeline: {
      show: true,
      axisType: 'category',
      autoPlay: false,
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

