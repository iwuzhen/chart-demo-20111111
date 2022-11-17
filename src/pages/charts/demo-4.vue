<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'

// import 'd3-array'
import { convertData, scale } from '~/hook/utils'

const demoData = [
  ['year', '天津', '广州', '武汉', '深圳', '南京', '长沙', '成都', '杭州', '上海', '北京'],
  [1980, 0, 0, 0, 0, 0, 0, 2, 1, 0, 10],
  [1981, 0, 0, 1, 0, 0, 0, 3, 1, 2, 11],
  [1982, 1, 0, 1, 0, 2, 1, 5, 1, 2, 20],
  [1983, 2, 0, 6, 0, 2, 1, 8, 2, 6, 33],
  [1984, 2, 0, 6, 0, 3, 1, 9, 2, 10, 51],
  [1985, 3, 1, 9, 1, 4, 3, 11, 3, 14, 66],
  [1986, 5, 3, 11, 1, 6, 3, 16, 5, 18, 90],
  [1987, 9, 3, 16, 2, 11, 6, 20, 12, 28, 107],
  [1988, 21, 5, 41, 2, 25, 11, 28, 29, 50, 153],
  [1989, 29, 8, 49, 3, 30, 11, 34, 39, 65, 187],
  [1990, 35, 16, 58, 4, 48, 17, 46, 50, 81, 242],
  [1991, 39, 25, 68, 4, 66, 28, 51, 64, 105, 304],
  [1992, 50, 35, 83, 4, 97, 39, 61, 80, 138, 384],
  [1993, 60, 42, 111, 6, 123, 56, 76, 95, 173, 515],
  [1994, 77, 56, 128, 6, 172, 78, 92, 108, 203, 648],
  [1995, 88, 67, 166, 7, 212, 99, 107, 124, 231, 756],
  [1996, 111, 82, 224, 9, 289, 145, 128, 147, 289, 997],
  [1997, 129, 106, 259, 10, 335, 197, 151, 180, 343, 1178],
  [1998, 154, 133, 345, 12, 397, 222, 176, 231, 419, 1527],
  [1999, 179, 158, 401, 17, 447, 244, 203, 264, 502, 1844],
  [2000, 232, 204, 480, 23, 543, 272, 256, 330, 657, 2242],
  [2001, 290, 264, 638, 35, 665, 326, 316, 405, 843, 2855],
  [2002, 425, 335, 837, 47, 808, 431, 411, 554, 1132, 3773],
  [2003, 581, 448, 1135, 73, 1063, 592, 546, 773, 1516, 4825],
  [2004, 850, 677, 1568, 105, 1478, 878, 781, 1218, 2217, 6367],
  [2005, 1238, 1098, 2357, 163, 2046, 1307, 1193, 1764, 3185, 8481],
  [2006, 1788, 1710, 3475, 248, 2957, 2041, 1773, 2512, 4453, 11890],
  [2007, 2458, 2473, 4998, 376, 4125, 2933, 2577, 3309, 5932, 15745],
  [2008, 3309, 3511, 6690, 531, 5576, 4085, 3446, 4283, 7928, 20735],
  [2009, 4296, 4687, 8734, 728, 7443, 5344, 4532, 5485, 10222, 26896],
  [2010, 5370, 6011, 10720, 1024, 9372, 6563, 5766, 6848, 12675, 33736],
  [2011, 6374, 7203, 12585, 1349, 11182, 7811, 6949, 8135, 14983, 40118],
  [2012, 7319, 8302, 14261, 1750, 12897, 8920, 7998, 9259, 17194, 46566],
  [2013, 8228, 9313, 15955, 2186, 14758, 10024, 9146, 10344, 19415, 53601],
  [2014, 9084, 10364, 17685, 2713, 16716, 11113, 10373, 11443, 21685, 61025],
  [2015, 9863, 11373, 19433, 3304, 18548, 12204, 11395, 12521, 23861, 68030],
  [2016, 10646, 12474, 21130, 4093, 20547, 13309, 12561, 13567, 26089, 75364],
  [2017, 11586, 13933, 23087, 5199, 22950, 14520, 13958, 14855, 28719, 83687],
  [2018, 12905, 15896, 25619, 6857, 26023, 16181, 15818, 16621, 32185, 94740],
  [2019, 14693, 18593, 28907, 9396, 30147, 18358, 18694, 19333, 37128, 109430],
  [2020, 16792, 21941, 32735, 12539, 35027, 20784, 21860, 22594, 42999, 125034],
  [2021, 19657, 26702, 38191, 17317, 41427, 24080, 26532, 27156, 51185, 145877],
  [2022, 21311, 29305, 41331, 19243, 45067, 26047, 29204, 29715, 55465, 156735],
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

  const options: echarts.EChartsOption[] = demoData.slice(1).map((item, index, obj): echarts.EChartsOption => {
    return {
      title: {
        text: `china ${item[0]}`,
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
              // color: '#fff',
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
          name: 'china',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          effectType: 'ripple',
          geoIndex: 0,
          rippleEffect: {
            brushType: 'fill',
          },
          showEffectOn: 'render',
          data: convertData(
            demoData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: demoData[index + 1][cityIndex + 1] as number,
              }
            }).filter(itemArray =>
              itemArray.value !== 0,
            ),
          ),
          label: {
            show: true,
            fontSize: 20,
            distance: 10,
            color: '#fff',
            position: 'right', // inside|right
          },
          labelLine: {
            show: true,
            length2: 5,
            lineStyle: {
              color: '#bbb',
            },
          },
          // itemStyle: {
          //   normal: {
          //     color: '#F4E925',
          //     shadowBlur: 10,
          //     shadowColor: '#333',
          //   },
          // },
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            // 线性映射到 0-100的范围
            return scale(val[2], 0, 156735, 5, 80)
          },
          // showEffectOn: 'emphasis',
          // rippleEffect: {
          //   brushType: 'stroke',
          // },
          // label: {
          //   formatter: '{b}',
          //   position: 'right',
          //   show: true,
          // },
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
        max: 160000,
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
        // geoIndex: 0,
        // itemStyle: {
        //   areaColor: '#eee',
        //   color: 'rgba(255,255,255,244)',
        //   borderColor: '#fff',
        //   // borderColor: '#000',
        //   borderWidth: 1,
        // },
        itemStyle: {
          normal: {
            areaColor: '#24CFF4',
            borderColor: '#53D9FF',
            borderWidth: 1.3,
            shadowBlur: 15,
            shadowColor: '#3a73c0',
            shadowOffsetX: 7,
            shadowOffsetY: 6,
          },
          emphasis: {
            areaColor: '#8dd7fc',
            borderWidth: 1.6,
            shadowBlur: 25,
          },
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
    backgroundColor: '#012248',
    // animationDuration: 2000,
    // animationDurationUpdate: 2000,
    // animationDelay: 2000,
    // animationDelayUpdate: 2000,
    animationEasing: 'linear',
    animationEasingUpdate: 'linear',
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

