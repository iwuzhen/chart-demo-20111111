<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import * as d3 from 'd3-geo'
import 'd3-array'
import { convertData, scale } from '~/hook/utils'

import { geoMapItemStyle, publicConfig } from '~/hook/sharedata'

const USAprojection = d3.geoAlbersUsa()

const chinaData = [
  ['year', '北京', '广州', '武汉', '上海', '深圳', '南京', '长沙', '成都', '杭州', '天津'],
  [1980, 6645, 95, 52, 3179, 111, 21, 231, 2, 107, 192],
  [1981, 7088, 98, 54, 3680, 124, 21, 298, 2, 119, 193],
  [1982, 7618, 99, 55, 4105, 131, 23, 368, 2, 132, 196],
  [1983, 8274, 103, 59, 4836, 149, 25, 417, 2, 137, 197],
  [1984, 8911, 110, 64, 5379, 181, 25, 451, 2, 157, 197],
  [1985, 9551, 114, 69, 5838, 197, 29, 485, 2, 192, 216],
  [1986, 10352, 127, 120, 5978, 224, 35, 542, 31, 251, 250],
  [1987, 11113, 140, 149, 6113, 246, 43, 601, 45, 332, 291],
  [1988, 11787, 168, 186, 6237, 272, 54, 651, 58, 448, 319],
  [1989, 12823, 240, 251, 6410, 312, 115, 722, 85, 620, 399],
  [1990, 13981, 313, 314, 6627, 340, 169, 773, 118, 757, 500],
  [1991, 15237, 389, 369, 6920, 388, 247, 834, 144, 898, 629],
  [1992, 16737, 467, 439, 7199, 437, 339, 901, 189, 1106, 755],
  [1993, 18513, 533, 498, 7452, 510, 429, 933, 222, 1261, 857],
  [1994, 20888, 623, 590, 7731, 588, 528, 957, 263, 1442, 966],
  [1995, 23407, 689, 684, 8104, 718, 636, 998, 302, 1600, 1032],
  [1996, 25839, 768, 768, 8766, 909, 754, 1022, 349, 1726, 1110],
  [1997, 28495, 861, 833, 9510, 1172, 864, 1034, 380, 1905, 1191],
  [1998, 31465, 986, 909, 10438, 1452, 1015, 1061, 434, 2073, 1278],
  [1999, 34913, 1179, 1028, 11300, 1835, 1414, 1102, 527, 2310, 1393],
  [2000, 38685, 1482, 1188, 12763, 2614, 1749, 1167, 660, 2701, 1595],
  [2001, 43317, 1902, 1491, 17949, 3649, 2279, 1252, 851, 3192, 1822],
  [2002, 49718, 2322, 1943, 23179, 5138, 2833, 1389, 1107, 3921, 2248],
  [2003, 58980, 3152, 2644, 27543, 7960, 3633, 1671, 1572, 5024, 3569],
  [2004, 69729, 4057, 3631, 33079, 13181, 4409, 2026, 2143, 6417, 5654],
  [2005, 83686, 5766, 4933, 41104, 20218, 5829, 2610, 3085, 8855, 9134],
  [2006, 100285, 7852, 6425, 52201, 33481, 7990, 3305, 4266, 12169, 13518],
  [2007, 122148, 10805, 8469, 65841, 55009, 10735, 4314, 5925, 16340, 18153],
  [2008, 154354, 14867, 11623, 84761, 87239, 14619, 5886, 8306, 22257, 23341],
  [2009, 197548, 20218, 15564, 106901, 126307, 19851, 8089, 11643, 29575, 29959],
  [2010, 254957, 27978, 22391, 138224, 173946, 27584, 12169, 16569, 41747, 38936],
  [2011, 327608, 38825, 31339, 176894, 228013, 37536, 16964, 23513, 55775, 50595],
  [2012, 425740, 53365, 43963, 226733, 292188, 51725, 25516, 34844, 74350, 66495],
  [2013, 556995, 72303, 59981, 286690, 364410, 68591, 35934, 48191, 98098, 87326],
  [2014, 715092, 94246, 79517, 349663, 446978, 88320, 46556, 62067, 121675, 111646],
  [2015, 907812, 120380, 103517, 423962, 539126, 113342, 59402, 80494, 149980, 143760],
  [2016, 1121298, 157395, 129378, 507873, 646737, 141702, 73464, 102880, 181484, 182660],
  [2017, 1364342, 210361, 162536, 603778, 777817, 176709, 90123, 130925, 219316, 224304],
  [2018, 1636794, 286536, 204008, 716877, 941475, 220105, 112080, 169288, 268782, 273722],
  [2019, 1933628, 365792, 253854, 835045, 1125768, 271218, 135601, 211332, 328104, 317979],
  [2020, 2034769, 384548, 269732, 862998, 1177225, 285910, 141970, 221632, 345186, 326107],
]

const usaData = [['year', '大波士顿', '休斯顿大都会区', '旧金山湾区', '达拉斯大都会区', '北卡三角研究区', '纽约'], [1980, 388032, 167488, 118771, 159006, 14741, 114468], [1981, 404842, 175849, 125363, 166648, 15631, 123266], [1982, 422229, 184780, 132951, 174772, 16371, 132504], [1983, 442666, 194566, 141460, 183421, 17138, 142935], [1984, 463995, 204205, 150247, 192448, 17898, 154387], [1985, 486314, 214285, 160428, 202288, 18751, 165936], [1986, 507977, 224373, 170393, 212960, 19530, 177884], [1987, 530253, 233812, 180472, 223811, 20387, 189115], [1988, 552668, 242505, 190444, 234982, 21156, 200211], [1989, 577269, 251652, 201415, 247292, 22058, 211857], [1990, 604353, 260842, 213930, 260341, 23108, 222704], [1991, 633926, 270606, 228113, 274338, 24296, 234237], [1992, 664686, 280534, 244334, 289664, 25570, 246093], [1993, 697666, 290895, 262317, 306542, 27028, 258158], [1994, 732763, 301741, 283315, 326613, 28763, 270104], [1995, 768040, 312146, 305777, 347073, 30860, 281515], [1996, 804961, 323160, 331287, 369311, 33131, 292725], [1997, 844125, 335062, 359727, 392965, 35585, 304852], [1998, 889346, 349693, 396086, 418375, 38685, 318434], [1999, 938604, 364379, 435722, 443861, 42457, 333062], [2000, 993023, 380725, 482728, 470397, 46758, 350586], [2001, 1055937, 398933, 541699, 498827, 51576, 373744], [2002, 1139950, 423076, 624046, 533748, 57969, 403957], [2003, 1231408, 451228, 720399, 569842, 65122, 434167], [2004, 1324845, 480561, 817074, 605857, 72538, 465139], [2005, 1416624, 509667, 913226, 639574, 79843, 495540], [2006, 1514031, 539092, 1017261, 675425, 87901, 524667], [2007, 1617961, 568833, 1126406, 712965, 96109, 553705], [2008, 1723892, 599493, 1238432, 749614, 105051, 581596], [2009, 1827091, 628490, 1345089, 783451, 113543, 607395], [2010, 1928219, 657491, 1454321, 816892, 122183, 631067], [2011, 2033928, 687136, 1565591, 851101, 131346, 654453], [2012, 2145607, 718727, 1685287, 887015, 141060, 677752], [2013, 2259187, 752204, 1818694, 925540, 151252, 701095], [2014, 2380028, 790096, 1973566, 967582, 162414, 726191], [2015, 2499768, 830341, 2124416, 1008134, 172766, 751311], [2016, 2623774, 873574, 2282758, 1050822, 182889, 781165], [2017, 2751516, 916649, 2443410, 1093806, 192898, 813800], [2018, 2871000, 956451, 2597100, 1134250, 202352, 844119], [2019, 2990024, 994793, 2755092, 1173681, 212392, 873792], [2020, 3029784, 1007723, 2811335, 1187360, 215834, 882686]]

// 根据数值调整国家上的点
const demoScale = (value: number) => {
  return scale(value, 0, 3029784, 5, 60)
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
    let sortCache: any[] = []
    chinaData[0].slice(1).forEach((v, subIndex) => {
      sortCache.push([v, item.slice(1)[subIndex]])
    })
    usaData[0].slice(1).forEach((v, subIndex) => {
      sortCache.push([v, usaData.slice(1)[index].slice(1)[subIndex]])
    })
    sortCache = sortCache.sort((a, b) => b[1] - a[1])
    return {
      title: [{
        text: `${item[0]}`,
        left: 860,
        bottom: 540,
        textStyle: {
          color: 'rgb(254,254,254,0.7)',
          fontSize: 46,
        },
      }, {
        text: '专利',
        left: 1,
        top: 1,
        textStyle: {
          color: 'rgb(254,254,254,0.3)',
          fontSize: 20,
        },
      }],

      legend: [
        {
          show: true,
          orient: 'vertical',
          data: sortCache.slice(0, 8).map(item => item[0]),
          // data: ['波士顿', '旧金山湾区', '北京', '达拉斯', '深圳', '休斯顿', '纽约', '上海', '广州'],
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
          data: sortCache.slice(8).map(item => item[0]),
          // data: ['杭州', '天津', '南京', '武汉', '成都', '北卡三角研究区', '长沙'],
          // z: 30,
          left: 1405,
          top: 764,
          textStyle: {
            color: 'rgb(255,255,255)',
            fontSize: 14,
          },

          padding: 2,
        },
      ],
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
        // height: 1600,
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
        max: 3229784,
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

    timeline: {
      show: true,
      axisType: 'category',
      autoPlay: false,
      loop: false,
      playInterval: publicConfig.playInterval,
      data: usaData.slice(1).map(item => item[0]),
    },
    options,
    animation: publicConfig.animation,
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

