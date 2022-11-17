<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'

// import 'd3-array'
import { convertData } from '~/hook/utils'

const demoData = [
  ['year', '北京', '广州', '武汉', '上海', '深圳', '南京', '长沙', '成都', '杭州', '天津'],
  [1980, 10, 0, 0, 0, 0, 0, 0, 2, 1, 0],
  [1981, 11, 0, 1, 2, 0, 0, 0, 3, 1, 0],
  [1982, 20, 0, 1, 2, 0, 2, 1, 5, 1, 1],
  [1983, 33, 0, 6, 6, 0, 2, 1, 8, 2, 2],
  [1984, 51, 0, 6, 10, 0, 3, 1, 9, 2, 2],
  [1985, 66, 1, 9, 14, 1, 4, 3, 11, 3, 3],
  [1986, 90, 3, 11, 18, 1, 6, 3, 16, 5, 5],
  [1987, 107, 3, 16, 28, 2, 11, 6, 20, 12, 9],
  [1988, 153, 5, 41, 50, 2, 25, 11, 28, 29, 21],
  [1989, 187, 8, 49, 65, 3, 30, 11, 34, 39, 29],
  [1990, 242, 16, 58, 81, 4, 48, 17, 46, 50, 35],
  [1991, 304, 25, 68, 105, 4, 66, 28, 51, 64, 39],
  [1992, 384, 35, 83, 138, 4, 97, 39, 61, 80, 50],
  [1993, 515, 42, 111, 173, 6, 123, 56, 76, 95, 60],
  [1994, 648, 56, 128, 203, 6, 172, 78, 92, 108, 77],
  [1995, 756, 67, 166, 231, 7, 212, 99, 107, 124, 88],
  [1996, 997, 82, 224, 289, 9, 289, 145, 128, 147, 111],
  [1997, 1178, 106, 259, 343, 10, 335, 197, 151, 180, 129],
  [1998, 1527, 133, 345, 419, 12, 397, 222, 176, 231, 154],
  [1999, 1844, 158, 401, 502, 17, 447, 244, 203, 264, 179],
  [2000, 2242, 204, 480, 657, 23, 543, 272, 256, 330, 232],
  [2001, 2855, 264, 638, 843, 35, 665, 326, 316, 405, 290],
  [2002, 3773, 335, 837, 1132, 47, 808, 431, 411, 554, 425],
  [2003, 4825, 448, 1135, 1516, 73, 1063, 592, 546, 773, 581],
  [2004, 6367, 677, 1568, 2217, 105, 1478, 878, 781, 1218, 850],
  [2005, 8481, 1098, 2357, 3185, 163, 2046, 1307, 1193, 1764, 1238],
  [2006, 11890, 1710, 3475, 4453, 248, 2957, 2041, 1773, 2512, 1788],
  [2007, 15745, 2473, 4998, 5932, 376, 4125, 2933, 2577, 3309, 2458],
  [2008, 20735, 3511, 6690, 7928, 531, 5576, 4085, 3446, 4283, 3309],
  [2009, 26896, 4687, 8734, 10222, 728, 7443, 5344, 4532, 5485, 4296],
  [2010, 33736, 6011, 10720, 12675, 1024, 9372, 6563, 5766, 6848, 5370],
  [2011, 40118, 7203, 12585, 14983, 1349, 11182, 7811, 6949, 8135, 6374],
  [2012, 46566, 8302, 14261, 17194, 1750, 12897, 8920, 7998, 9259, 7319],
  [2013, 53601, 9313, 15955, 19415, 2186, 14758, 10024, 9146, 10344, 8228],
  [2014, 61025, 10364, 17685, 21685, 2713, 16716, 11113, 10373, 11443, 9084],
  [2015, 68030, 11373, 19433, 23861, 3304, 18548, 12204, 11395, 12521, 9863],
  [2016, 75364, 12474, 21130, 26089, 4093, 20547, 13309, 12561, 13567, 10646],
  [2017, 83687, 13933, 23087, 28719, 5199, 22950, 14520, 13958, 14855, 11586],
  [2018, 94740, 15896, 25619, 32185, 6857, 26023, 16181, 15818, 16621, 12905],
  [2019, 109430, 18593, 28907, 37128, 9396, 30147, 18358, 18694, 19333, 14693],
  [2020, 125034, 21941, 32735, 42999, 12539, 35027, 20784, 21860, 22594, 16792],
  [2021, 145877, 26702, 38191, 51185, 17317, 41427, 24080, 26532, 27156, 19657],
  [2022, 156735, 29305, 41331, 55465, 19243, 45067, 26047, 29204, 29715, 21311],
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

function scale(number: number, inMin: number, inMax: number, outMin: number, outMax: number): number {
  return (number - inMin) * (outMax - outMin) / (inMax - inMin) + outMin
}

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
          data: convertData(
            demoData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: demoData[index + 1][cityIndex + 1] as number,
              }
            }),
          ),
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            // 线性映射到 0-100的范围
            return scale(val[2], 0, 156735, 5, 100)
          },
          // showEffectOn: 'emphasis',
          rippleEffect: {
            brushType: 'stroke',
          },
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

