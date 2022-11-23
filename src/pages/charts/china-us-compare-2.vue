<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import * as d3 from 'd3-geo'
import 'd3-array'
import { convertData, scale } from '~/hook/utils'

import { geoMapItemStyle } from '~/hook/sharedata'

const USAprojection = d3.geoAlbersUsa()

const chinaData = [
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
  [2022, 156735, 29305, 41331, 55464, 19243, 45067, 26047, 29204, 29715, 21311],
]

const usaData = [
  ['year', '北卡三角研究区', '波士顿', '休斯顿', '达拉斯', '纽约', '旧金山湾区'],
  [1980, 424, 1730, 610, 182, 1513, 835],
  [1981, 460, 1898, 668, 208, 1655, 906],
  [1982, 498, 2077, 731, 239, 1818, 1008],
  [1983, 542, 2278, 784, 277, 1987, 1129],
  [1984, 576, 2512, 846, 308, 2151, 1258],
  [1985, 631, 2745, 943, 339, 2363, 1375],
  [1986, 688, 3008, 1042, 375, 2584, 1530],
  [1987, 765, 3316, 1184, 415, 2840, 1730],
  [1988, 888, 3732, 1364, 474, 3182, 2010],
  [1989, 987, 4225, 1601, 564, 3568, 2376],
  [1990, 1166, 4816, 1930, 647, 3971, 2751],
  [1991, 1344, 5543, 2268, 726, 4423, 3146],
  [1992, 1558, 6312, 2633, 815, 4906, 3640],
  [1993, 1739, 7020, 2993, 937, 5369, 4145],
  [1994, 1991, 7784, 3390, 1053, 5859, 4714],
  [1995, 2241, 8538, 3739, 1165, 6333, 5196],
  [1996, 2504, 9258, 4135, 1287, 6831, 5747],
  [1997, 2783, 10033, 4530, 1429, 7375, 6386],
  [1998, 3067, 10807, 4925, 1555, 7894, 6974],
  [1999, 3331, 11613, 5331, 1717, 8429, 7630],
  [2000, 3606, 12504, 5782, 1880, 9083, 8293],
  [2001, 3878, 13383, 6186, 2052, 9736, 8945],
  [2002, 4221, 14348, 6669, 2220, 10520, 9689],
  [2003, 4606, 15433, 7182, 2388, 11408, 10561],
  [2004, 4966, 16659, 7766, 2579, 12457, 11492],
  [2005, 5371, 18147, 8453, 2830, 13527, 12550],
  [2006, 5860, 19670, 9198, 3090, 14770, 13682],
  [2007, 6397, 21334, 9965, 3422, 16070, 14844],
  [2008, 6960, 23113, 10807, 3787, 17465, 16232],
  [2009, 7585, 25002, 11676, 4165, 18993, 17777],
  [2010, 8297, 27236, 12658, 4664, 20718, 19509],
  [2011, 9031, 29467, 13663, 5179, 22510, 21190],
  [2012, 9866, 31985, 14696, 5760, 24509, 23097],
  [2013, 10745, 34539, 15907, 6343, 26682, 25198],
  [2014, 11749, 37419, 17172, 6987, 28997, 27436],
  [2015, 12704, 40346, 18476, 7594, 31524, 30055],
  [2016, 13815, 43653, 19816, 8287, 34305, 32976],
  [2017, 15093, 47363, 21446, 9132, 37436, 36537],
  [2018, 16652, 52035, 23492, 10129, 41308, 41115],
  [2019, 18601, 57663, 25864, 11396, 46022, 47693],
  [2020, 20762, 63669, 28704, 12707, 51242, 54443],
  [2021, 23097, 70486, 31967, 14278, 56870, 61545],
  [2022, 23946, 73219, 33318, 14924, 58940, 63614],
]

// 根据数值调整国家上的点
const demoScale = (value: number) => {
  return scale(value, 0, 156735, 5, 60)
}

onMounted(async () => {
  let response = await fetch('/data/usa.json')
  echarts.registerMap('USA', await response.json())

  response = await fetch('/data/100000_full.json')
  echarts.registerMap('china', await response.json(), {
    南海诸岛: {
      left: 80,
      top: 20,
      width: 5,
    },
  })

  const elem = document.getElementById('chartID')
  // let chartObj = echarts.init(elem);
  if (!elem)
    return

  const options: echarts.EChartsOption[] = chinaData.slice(1).map((item, index, obj): echarts.EChartsOption => {
    return {
      title: {
        text: `${item[0]}`,
        left: 860,
        bottom: 540,
        textStyle: {
          color: 'rgb(254,254,254,0.7)',
          fontSize: 46,
        },
      },
      series: [
        ...chinaData[0].slice(1).map((cityName, cityIndex): echarts.SeriesOption => {
          return {
            id: cityName,
            name: cityName,
            type: 'line',
            xAxisIndex: 0,
            yAxisIndex: 0,
            showSymbol: false,
            labelLayout: {
              // moveOverlap: 'shiftX',
            },
            // endLabel: {
            //   show: true,
            //   fontSize: 16,
            //   formatter(params: any) {
            //     // console.log(params)
            //     return `${params.seriesId}: ${params.value[1]}`
            //   },
            // },
            data: chinaData.slice(1).map((dataItem, currentIndex) => {
              if (currentIndex <= index)
                return [dataItem[0], dataItem[cityIndex + 1]]
              else
                return [dataItem[0], '-']
            }),
          }
        }), ...usaData[0].slice(1).map((cityName, cityIndex): echarts.SeriesOption => {
          return {
            id: cityName,
            name: cityName,
            type: 'line',
            xAxisIndex: 0,
            yAxisIndex: 0,
            showSymbol: false,
            labelLayout: {
              // moveOverlap: 'shiftX',
            },
            // endLabel: {
            //   show: true,
            //   fontSize: 16,
            //   formatter(params: any) {
            //     // console.log(params)
            //     return `${params.seriesId}: ${params.value[1]}`
            //   },
            // },
            data: usaData.slice(1).map((dataItem, currentIndex) => {
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
          type: 'scatter',
          coordinateSystem: 'geo',

          // effectType: 'ripple',
          // rippleEffect: {
          //   brushType: 'fill',
          // },
          labelLayout(params: any): any {
            const ret = {
              y: params.labelRect.y,
              x: params.labelRect.x,
              // x: params.rect.x,
              // moveOverlap: 'shiftX',
              align: 'left',
              verticalAlign: 'middle',
            }
            if (params.text === '北京')
              ret.y -= 10
            if (params.text === '南京')
              ret.y -= 10
            if (params.text === '天津')
              ret.y += 20
            if (params.text === '杭州')
              ret.y += 20
            if (params.text === '深圳')
              ret.y += 20
            if (params.text === '广州')
              ret.y -= 10
            return ret
          },
          geoIndex: 0,
          data: convertData(
            chinaData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: chinaData[index + 1][cityIndex + 1] as number,
              }
            }),
            // .filter(arrayItem => ['上海'].includes(arrayItem.name)),
          ),
          // labelLine: {
          //   show: true,
          //   length2: 5,
          //   lineStyle: {
          //     color: '#bbb',
          //   },
          // },
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            // 线性映射到 0-100的范围
            return demoScale(val[2])
          },
          // showEffectOn: 'render',
          label: {
            formatter: '{b}',
            fontSize: 20,
            color: '#fff',
            position: 'right',
            show: true,
          },
          itemStyle: {
            color: 'rgb(238,72,28)',
            shadowBlur: 10,
            shadowColor: '#333',
          },
        }, {
          id: 'USA',
          name: 'USA',
          type: 'scatter',
          coordinateSystem: 'geo',
          // effectType: 'ripple',
          geoIndex: 1,
          data: convertData(
            usaData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: usaData[index + 1][cityIndex + 1] as number,
              }
            }),
            // .filter(arrayItem => ['纽约'].includes(arrayItem.name)),
          ),
          // labelLine: {
          //   show: true,
          //   length2: 5,
          //   lineStyle: {
          //     color: '#bbb',
          //   },
          // },
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            // 线性映射到 0-100的范围
            return demoScale(val[2])
          },
          // showEffectOn: 'render',
          // rippleEffect: {
          //   brushType: 'stroke',
          // },
          label: {
            formatter: '{b}',
            fontSize: 20,
            color: '#fff',
            position: 'left',
            show: true,
          },
          itemStyle: {
            // color: 'rgb(139,183,255)',
            color: 'yellow',
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
    animationDuration: 500,
    grid: [
      {
        show: false,
        id: 0,
        right: 700,
        bottom: 100,
        // right: 1200,
        height: 200,
        // height: 2200,
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
        max: 180000,
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
        map: 'china',
        left: 20,
        zoom: 1.1,
        roam: false,
        emphasis: {
          disabled: true,
        },
        center: [100, 30],
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
      },
    ],
    series: [

    ],
    color: ['#ee6666', '#fac858', '#91cc75', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#5470c6', '#ea7ccc'],

    legend: [
      {
        show: true,
        orient: 'vertical',
        data: ['北京', '波士顿', '旧金山湾区', '纽约', '上海', '南京', '武汉', '休斯顿'],
        // z: 30,
        right: 550,
        top: 764,
        textStyle: {
          color: 'rgb(255,255,255)',
          fontSize: 14,
        },
        // itemStyle: 10,
        padding: 2,
        // itemStyle:{

      // }
      }, {
        show: true,
        orient: 'vertical',
        data: ['杭州', '广州', '成都', '长沙', '北卡三角研究区', '天津', '深圳', '达拉斯'],
        // z: 30,
        right: 405,
        top: 764,
        textStyle: {
          color: 'rgb(255,255,255)',
          fontSize: 14,
        },
        // itemStyle: 10,
        padding: 2,
        // itemStyle:{

      // }
      },
    ],
    timeline: {
      show: true,
      axisType: 'category',
      autoPlay: false,
      loop: false,
      playInterval: 500,
      data: usaData.slice(1).map(item => item[0]),
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

