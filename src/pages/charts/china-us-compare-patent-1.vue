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
  ['year', '粤港澳湾区', '旧金山湾区', '上海', '纽约'],
  [1980, 776, 118771, 3179, 114468],
  [1981, 870, 125363, 3680, 123266],
  [1982, 976, 132951, 4105, 132504],
  [1983, 1212, 141460, 4836, 142935],
  [1984, 1448, 150247, 5379, 154387],
  [1985, 1687, 160428, 5838, 165936],
  [1986, 1927, 170393, 5978, 177884],
  [1987, 2159, 180472, 6113, 189115],
  [1988, 2422, 190444, 6237, 200211],
  [1989, 2737, 201415, 6410, 211857],
  [1990, 3038, 213930, 6627, 222704],
  [1991, 3420, 228113, 6920, 234237],
  [1992, 3804, 244334, 7199, 246093],
  [1993, 4259, 262317, 7452, 258158],
  [1994, 4800, 283315, 7731, 270104],
  [1995, 5354, 305777, 8104, 281515],
  [1996, 6028, 331287, 8766, 292725],
  [1997, 6828, 359727, 9510, 304852],
  [1998, 7805, 396086, 10438, 318434],
  [1999, 9105, 435722, 11300, 333062],
  [2000, 11161, 482728, 12763, 350586],
  [2001, 13841, 541699, 17949, 373744],
  [2002, 17196, 624046, 23179, 403957],
  [2003, 22393, 720399, 27543, 434167],
  [2004, 30411, 817074, 33079, 465139],
  [2005, 41889, 913226, 41104, 495540],
  [2006, 61318, 1017261, 52201, 524667],
  [2007, 91444, 1126406, 65841, 553705],
  [2008, 135985, 1238432, 84761, 581596],
  [2009, 190482, 1345089, 106901, 607395],
  [2010, 261647, 1454321, 138224, 631067],
  [2011, 348184, 1565591, 176894, 654453],
  [2012, 455371, 1685287, 226733, 677752],
  [2013, 582992, 1818694, 286690, 701095],
  [2014, 729089, 1973566, 349663, 726191],
  [2015, 904224, 2124416, 423962, 751311],
  [2016, 1119808, 2282758, 507873, 781165],
  [2017, 1405056, 2443410, 603778, 813800],
  [2018, 1787377, 2597100, 716877, 844119],
  [2019, 2191632, 2755092, 835045, 873792],
  [2020, 2299279, 2811335, 862998, 882686],
]

// 粤港澳湾区数据
const gbaCityData = [
  ['year', '佛山', '深圳', '中山', '广州', '东莞', '珠海', '江门', '惠州', '肇庆', '香港', '澳门'],
  [1980, 366, 111, 21, 95, 32, 64, 0, 3, 0, 84, 0],
  [1981, 403, 124, 23, 98, 44, 74, 0, 3, 0, 101, 0],
  [1982, 438, 131, 45, 99, 51, 100, 0, 3, 0, 109, 0],
  [1983, 566, 149, 64, 103, 66, 139, 0, 3, 0, 122, 0],
  [1984, 685, 181, 79, 110, 71, 189, 0, 3, 0, 130, 0],
  [1985, 820, 197, 85, 114, 77, 245, 0, 3, 0, 146, 0],
  [1986, 965, 224, 89, 127, 83, 281, 0, 3, 0, 155, 0],
  [1987, 1109, 246, 92, 140, 93, 317, 0, 3, 0, 159, 0],
  [1988, 1246, 272, 101, 168, 104, 362, 0, 4, 0, 165, 0],
  [1989, 1382, 312, 109, 240, 112, 402, 1, 5, 0, 175, 0],
  [1990, 1499, 340, 116, 313, 121, 454, 2, 8, 0, 187, 0],
  [1991, 1642, 388, 122, 389, 128, 532, 3, 22, 0, 196, 0],
  [1992, 1810, 437, 131, 467, 134, 579, 3, 37, 1, 208, 0],
  [1993, 2025, 510, 135, 533, 141, 654, 4, 47, 1, 216, 0],
  [1994, 2289, 588, 146, 623, 159, 711, 11, 56, 2, 224, 0],
  [1995, 2559, 718, 154, 689, 169, 762, 13, 66, 3, 233, 0],
  [1996, 2794, 909, 161, 768, 212, 855, 13, 73, 4, 254, 0],
  [1997, 3045, 1172, 172, 861, 242, 977, 13, 79, 4, 284, 0],
  [1998, 3317, 1452, 199, 986, 271, 1187, 18, 92, 6, 310, 0],
  [1999, 3579, 1835, 245, 1179, 344, 1441, 34, 115, 15, 364, 0],
  [2000, 3996, 2614, 285, 1482, 472, 1714, 40, 152, 27, 450, 0],
  [2001, 4429, 3649, 380, 1902, 626, 2066, 55, 199, 55, 573, 0],
  [2002, 4887, 5138, 467, 2322, 885, 2490, 81, 251, 75, 717, 0],
  [2003, 5322, 7960, 568, 3152, 1140, 2945, 137, 337, 94, 912, 0],
  [2004, 5925, 13181, 727, 4057, 1457, 3362, 189, 477, 113, 1182, 0],
  [2005, 6933, 20218, 1069, 5766, 1959, 3817, 267, 712, 149, 1549, 0],
  [2006, 8431, 33481, 1531, 7852, 2660, 4550, 377, 1176, 189, 2063, 0],
  [2007, 10679, 55009, 2183, 10805, 3762, 5700, 522, 1552, 229, 2715, 0],
  [2008, 13941, 87239, 3208, 14867, 5824, 7019, 758, 2058, 309, 3656, 2],
  [2009, 17632, 126307, 4370, 20218, 8587, 8233, 1054, 2765, 396, 5108, 2],
  [2010, 22426, 173946, 6067, 27978, 14684, 10183, 1601, 4008, 597, 6491, 2],
  [2011, 28792, 228013, 8393, 38825, 22706, 12877, 2481, 6115, 983, 8118, 2],
  [2012, 37138, 292188, 11052, 53365, 32321, 16985, 3600, 10916, 1624, 10533, 2],
  [2013, 47599, 364410, 14529, 72303, 46859, 22442, 4876, 16184, 2270, 13044, 2],
  [2014, 62172, 446978, 18217, 94246, 60668, 29213, 6244, 23462, 2816, 16335, 3],
  [2015, 82862, 539126, 22813, 120380, 78273, 38067, 8245, 33091, 3336, 19784, 6],
  [2016, 108100, 646737, 28295, 157395, 100767, 49781, 10884, 44259, 3994, 23298, 17],
  [2017, 144572, 777817, 35310, 210361, 133597, 65697, 14941, 58135, 4955, 27026, 203],
  [2018, 192673, 941475, 45215, 286536, 180853, 88887, 21832, 75303, 6537, 31839, 653],
  [2019, 235467, 1125768, 53296, 365792, 229580, 115961, 27017, 93694, 8035, 37114, 1243],
  [2020, 245297, 1177225, 54781, 384548, 243802, 125788, 27409, 98812, 8230, 38464, 1451],
  [2021, 245297, 1177225, 54781, 384548, 243802, 125788, 27409, 98812, 8230, 38464, 1451],
  [2022, 245297, 1177225, 54781, 384548, 243802, 125788, 27409, 98812, 8230, 38464, 1451],
]

const sbaCityData = [
  ['year', 'Sunnyvale', 'Mountain View', 'Fremont', 'Richmond', 'Palo Alto', 'San Jose', 'San Francisco', 'Santa Clara', 'Newark', 'Concord'],
  [1980, 834, 1993, 912, 19795, 8865, 2639, 2782, 3794, 3459, 1504],
  [1981, 910, 2105, 1018, 20633, 9337, 2903, 2928, 4089, 3708, 1544],
  [1982, 1020, 2286, 1165, 21669, 9825, 3208, 3116, 4476, 3932, 1589],
  [1983, 1118, 2478, 1344, 22674, 10381, 3542, 3263, 4934, 4227, 1641],
  [1984, 1254, 2632, 1547, 23577, 10960, 3968, 3429, 5404, 4489, 1703],
  [1985, 1529, 2892, 1793, 24591, 11657, 4556, 3581, 5916, 4863, 1769],
  [1986, 1929, 3129, 2054, 25541, 12287, 5193, 3776, 6455, 5171, 1838],
  [1987, 2438, 3347, 2343, 26331, 13022, 5976, 3996, 7148, 5591, 1922],
  [1988, 2881, 3528, 2578, 27093, 13860, 6831, 4207, 7941, 6326, 2000],
  [1989, 3334, 3707, 2938, 27910, 14918, 7764, 4451, 8897, 6970, 2100],
  [1990, 3829, 3941, 3305, 28719, 16287, 8721, 4758, 10222, 7779, 2173],
  [1991, 4396, 4150, 3761, 29824, 17825, 9856, 5076, 11747, 8447, 2278],
  [1992, 4984, 4334, 4569, 30972, 19724, 11318, 5439, 13520, 9313, 2392],
  [1993, 5769, 4571, 5523, 32119, 21808, 12964, 5880, 15511, 10151, 2513],
  [1994, 6665, 4868, 6634, 33302, 24245, 14901, 6381, 18152, 11075, 2671],
  [1995, 7809, 5366, 7714, 34299, 27150, 17135, 7073, 21194, 11787, 2828],
  [1996, 9060, 5970, 8739, 35266, 30315, 20181, 7827, 25063, 12600, 3075],
  [1997, 10519, 6731, 10032, 36250, 33486, 23778, 8699, 29521, 13430, 3284],
  [1998, 12572, 7903, 11580, 37336, 37410, 28844, 9911, 35595, 14279, 3600],
  [1999, 15046, 9400, 13091, 38408, 41719, 34366, 11316, 42730, 15029, 3990],
  [2000, 18088, 11258, 14800, 39652, 46621, 41449, 13189, 51468, 15842, 4378],
  [2001, 21695, 13382, 17023, 40932, 52447, 51182, 15416, 63371, 16881, 4835],
  [2002, 26870, 16369, 19956, 42540, 62237, 64815, 19089, 79292, 18174, 5312],
  [2003, 32362, 20165, 23181, 44389, 74760, 80673, 22806, 98731, 19397, 5862],
  [2004, 37692, 24141, 26518, 46566, 86246, 98212, 26952, 118103, 20701, 6396],
  [2005, 42937, 28181, 29770, 48953, 96881, 116779, 31169, 137039, 22021, 6954],
  [2006, 48782, 32883, 33032, 51721, 106761, 138462, 35552, 157805, 23479, 7546],
  [2007, 55640, 38069, 36487, 54524, 114856, 162125, 40055, 178517, 25010, 8165],
  [2008, 62549, 43532, 39719, 57394, 122638, 187160, 45521, 197113, 26772, 8890],
  [2009, 68902, 48882, 42778, 59898, 129965, 209815, 50520, 214710, 28339, 9516],
  [2010, 74961, 54847, 46112, 62376, 137290, 232285, 56343, 231668, 29879, 10195],
  [2011, 81056, 61223, 49555, 64751, 144314, 255880, 61601, 248249, 31369, 10806],
  [2012, 87471, 69365, 53243, 67127, 151452, 280298, 67685, 265325, 32906, 11436],
  [2013, 94234, 79382, 57511, 69596, 159201, 306863, 74738, 284689, 34621, 12045],
  [2014, 101907, 92243, 61954, 72193, 168276, 337095, 83929, 309235, 36411, 12627],
  [2015, 109445, 105733, 66100, 74649, 178052, 363576, 93329, 333102, 37889, 13258],
  [2016, 116891, 118717, 69514, 77186, 188619, 389155, 104585, 361303, 39455, 13872],
  [2017, 124344, 133772, 72801, 79944, 199539, 411717, 116810, 392093, 41063, 14613],
  [2018, 131587, 148319, 75899, 82662, 211542, 431802, 129166, 420816, 42442, 15337],
  [2019, 139206, 162290, 78890, 85611, 225492, 452367, 143518, 447725, 43939, 16058],
  [2020, 141840, 167478, 79931, 86559, 230925, 459143, 149104, 456934, 44399, 16287],
  [2021, 141840, 167478, 79931, 86559, 230925, 459143, 149104, 456934, 44399, 16287],
  [2022, 141840, 167478, 79931, 86559, 230925, 459143, 149104, 456934, 44399, 16287],
]

// 根据数值调整国家上的点
const demoScale = (value: number) => {
  return scale(value, 0, 2811335, 5, 60)
}

// 根据数值调整小地图上的点
const demoScaleMinMap = (value: number) => {
  return scale(value, 0, 1277225, 10, 60)
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
        text: `${item[0]}`,
        left: 860,
        bottom: 540,
        textStyle: {
          color: 'rgb(254,254,254,0.7)',
          fontSize: 46,
        },
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
          type: 'scatter',
          coordinateSystem: 'geo',
          // effectType: 'ripple',
          // rippleEffect: {
          //   brushType: 'fill',
          // },
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
            demoData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: demoData[index + 1][cityIndex + 1] as number,
              }
            }).filter(arrayItem => ['纽约'].includes(arrayItem.name)),
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
            color: 'rgb(139,183,255)',
            shadowBlur: 10,
            shadowColor: '#333',
          },
        },
        {
          id: 'ChinaS',
          name: 'ChinaS',
          type: 'scatter',
          coordinateSystem: 'geo',
          // effectType: 'ripple',
          // rippleEffect: {
          //   brushType: 'fill',
          // },
          geoIndex: 2,
          data: convertData(
            gbaCityData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: gbaCityData[index + 1][cityIndex + 1] as number,
              }
            // }).filter(arrayItem => ['粤港澳湾区'].includes(arrayItem.name)),
            }).filter(arrayItem => arrayItem.value > 0),
          ),
          labelLine: {
            show: true,
            length2: 5,
            lineStyle: {
              color: '#bbb',
            },
          },
          // symbol: 'pin',
          encode: {
            value: 2,
          },
          symbolSize(val: number[]) {
            // 线性映射到 0-100的范围
            return demoScaleMinMap(val[2])
          },
          // showEffectOn: 'render',
          label: {
            formatter: '{b}',
            fontSize: 20,
            // color: 'red',
            position: 'right',
            show: true,
          },
          itemStyle: {
            color: 'rgb(238,72,28)',
            // shadowBlur: 10,
            // shadowColor: '#AD3B1C',
          },
        }, {
          id: 'USAS',
          name: 'USAS',
          type: 'scatter',
          coordinateSystem: 'geo',
          geoIndex: 3,
          // symbol: 'pin',
          data: convertData(
            sbaCityData[0].slice(1).map((cityName, cityIndex): CityData => {
              return {
                name: cityName as string,
                value: sbaCityData[index + 1][cityIndex + 1] as number,
              }
            // }).filter(arrayItem => ['旧金山湾区'].includes(arrayItem.name)),
            }),
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
            return demoScaleMinMap(val[2])
          },
          label: {
            formatter: '{b}',
            fontSize: 20,
            // color: 'red',
            position: 'right',
            show: true,
          },
          itemStyle: {
            color: 'rgb(139,183,255)',
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
        max: 3011335,
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
        center: ['-3%', '25%'],
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
        layoutCenter: [268, 624],
        layoutSize: 455,
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
          disabled: false,
        },
        layoutCenter: [1485, 713],
        layoutSize: 443,
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

      {
        id: 'ChinaPoint',
        type: 'scatter',
        coordinateSystem: 'geo',
        geoIndex: 0,
        data: [{
          name: '粤港澳大湾区',
          value: [113.541389, 22.195833],
        }],

        // labelLine: {
        //   show: true,
        //   length2: 20,
        //   lineStyle: {
        //     color: '#bbb',
        //   },
        // },
        symbol: 'circle',
        symbolSize: 40,
        label: {
          formatter: '{b}',
          fontSize: 20,
          color: 'rgba(254, 254, 254, 1)',
          position: 'right',
          show: true,
        },
        itemStyle: {
          color: 'rgba(255, 255, 255, 0)',
          borderWidth: 2,
          borderColor: 'rgba(254, 254, 254, 1)',
          borderType: 'dashed',
          shadowBlur: 0,
          opacity: 0.6,
          shadowColor: '#333',
        },
      },
      {
        id: 'USPoint',
        type: 'scatter',
        coordinateSystem: 'geo',
        geoIndex: 1,
        data: [{
          name: '旧金山湾区',
          value: [-122.28, 37.75],
        }],

        // labelLine: {
        //   show: true,
        //   length2: 2,
        //   lineStyle: {
        //     color: '#bbb',
        //   },
        // },
        symbol: 'circle',
        symbolSize: 40,
        // showEffectOn: 'render',
        label: {
          formatter: '{b}',
          fontSize: 20,
          color: 'rgba(254, 254, 254, 1)',
          position: 'left',
          show: true,
        },
        itemStyle: {
          color: 'rgba(255, 255, 255, 0)',
          borderWidth: 2,
          borderColor: 'rgba(254, 254, 254, 1)',
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
              x1: 575,
              y1: 677,
              x2: 500,
              y2: 669,
            },
            style: {
              lineWidth: 2,
              stroke: 'rgba(254, 254, 254, 0.7)',
            },
            z: 30,
          }, {
            type: 'line',
            shape: {
              x1: 1145,
              y1: 499,
              x2: 1285,
              y2: 585,
            },
            style: {
              lineWidth: 2,
              stroke: 'rgba(254, 254, 254, 0.7)',
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

