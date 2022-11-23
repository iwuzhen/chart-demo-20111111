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
  [1980, 19, 835, 0, 1513],
  [1981, 23, 906, 2, 1655],
  [1982, 27, 1008, 2, 1818],
  [1983, 38, 1129, 6, 1987],
  [1984, 45, 1258, 10, 2151],
  [1985, 57, 1375, 14, 2363],
  [1986, 67, 1530, 18, 2584],
  [1987, 73, 1730, 28, 2840],
  [1988, 93, 2010, 50, 3182],
  [1989, 121, 2376, 65, 3568],
  [1990, 155, 2751, 81, 3971],
  [1991, 204, 3146, 105, 4423],
  [1992, 255, 3640, 138, 4906],
  [1993, 338, 4145, 173, 5369],
  [1994, 514, 4714, 203, 5859],
  [1995, 714, 5196, 231, 6333],
  [1996, 1013, 5747, 289, 6831],
  [1997, 1337, 6386, 343, 7375],
  [1998, 1742, 6974, 419, 7894],
  [1999, 2137, 7630, 502, 8429],
  [2000, 2570, 8293, 657, 9083],
  [2001, 3124, 8945, 843, 9736],
  [2002, 3768, 9689, 1132, 10520],
  [2003, 4513, 10561, 1516, 11408],
  [2004, 5532, 11492, 2217, 12457],
  [2005, 6758, 12550, 3185, 13527],
  [2006, 8378, 13682, 4453, 14770],
  [2007, 10164, 14844, 5932, 16070],
  [2008, 12381, 16232, 7928, 17465],
  [2009, 14876, 17777, 10222, 18993],
  [2010, 17718, 19509, 12675, 20718],
  [2011, 20463, 21190, 14983, 22510],
  [2012, 23233, 23097, 17194, 24509],
  [2013, 25986, 25198, 19415, 26682],
  [2014, 29067, 27436, 21685, 28997],
  [2015, 32102, 30055, 23861, 31524],
  [2016, 35650, 32976, 26089, 34305],
  [2017, 40076, 36537, 28719, 37436],
  [2018, 45890, 41115, 32185, 41308],
  [2019, 53927, 47693, 37128, 46022],
  [2020, 63671, 54443, 42999, 51242],
  [2021, 77458, 61545, 51185, 56870],
  [2022, 83879, 63614, 55464, 58940],
]

// 粤港澳湾区数据
const gbaCityData = [
  ['year', '广州', '深圳', '香港', '澳门', '东莞', '佛山', '珠海', '惠州', '江门', '肇庆', '中山'],
  [1980, 0, 0, 18, 1, 0, 0, 0, 0, 0, 0, 0],
  [1981, 0, 0, 22, 1, 0, 0, 0, 0, 0, 0, 0],
  [1982, 0, 0, 26, 1, 0, 0, 0, 0, 0, 0, 0],
  [1983, 0, 0, 37, 1, 0, 0, 0, 0, 0, 0, 0],
  [1984, 0, 0, 44, 1, 0, 0, 0, 0, 0, 0, 0],
  [1985, 1, 1, 54, 1, 0, 0, 0, 0, 0, 0, 0],
  [1986, 3, 1, 62, 1, 0, 0, 0, 0, 0, 0, 0],
  [1987, 3, 2, 67, 1, 0, 0, 0, 0, 0, 0, 0],
  [1988, 5, 2, 85, 1, 0, 0, 0, 0, 0, 0, 0],
  [1989, 8, 3, 109, 1, 0, 0, 0, 0, 0, 0, 0],
  [1990, 16, 4, 134, 1, 0, 0, 0, 0, 0, 0, 0],
  [1991, 25, 4, 175, 1, 0, 0, 0, 0, 0, 0, 0],
  [1992, 35, 4, 218, 1, 0, 0, 0, 0, 0, 0, 0],
  [1993, 42, 6, 293, 1, 0, 0, 0, 0, 0, 0, 0],
  [1994, 56, 6, 453, 2, 1, 1, 0, 0, 0, 0, 0],
  [1995, 67, 7, 637, 3, 3, 1, 0, 0, 1, 0, 0],
  [1996, 82, 9, 916, 5, 3, 1, 0, 1, 2, 0, 0],
  [1997, 106, 10, 1212, 7, 3, 1, 1, 2, 2, 0, 0],
  [1998, 133, 12, 1590, 9, 3, 1, 1, 2, 2, 0, 0],
  [1999, 158, 17, 1957, 11, 3, 2, 1, 2, 2, 0, 0],
  [2000, 204, 23, 2338, 13, 6, 3, 1, 2, 4, 0, 0],
  [2001, 264, 35, 2811, 18, 7, 5, 1, 3, 5, 2, 0],
  [2002, 335, 47, 3361, 21, 9, 9, 1, 4, 8, 5, 0],
  [2003, 448, 73, 3955, 34, 9, 11, 1, 4, 10, 5, 0],
  [2004, 677, 105, 4699, 42, 13, 18, 1, 4, 12, 8, 0],
  [2005, 1098, 163, 5422, 65, 20, 32, 1, 8, 12, 11, 0],
  [2006, 1710, 248, 6309, 89, 33, 48, 3, 14, 14, 16, 0],
  [2007, 2473, 376, 7155, 112, 56, 69, 4, 18, 17, 26, 0],
  [2008, 3511, 531, 8112, 151, 81, 88, 8, 29, 22, 35, 0],
  [2009, 4687, 728, 9199, 205, 100, 107, 12, 36, 23, 48, 0],
  [2010, 6011, 1024, 10371, 263, 131, 140, 15, 52, 26, 57, 0],
  [2011, 7203, 1349, 11509, 359, 152, 168, 21, 60, 29, 75, 1],
  [2012, 8302, 1750, 12728, 450, 176, 191, 28, 69, 41, 84, 2],
  [2013, 9313, 2186, 13920, 607, 217, 214, 32, 91, 45, 94, 2],
  [2014, 10364, 2713, 15331, 810, 243, 229, 39, 106, 48, 106, 2],
  [2015, 11373, 3304, 16768, 994, 266, 239, 45, 118, 51, 111, 3],
  [2016, 12474, 4093, 18363, 1269, 302, 262, 50, 129, 53, 116, 3],
  [2017, 13933, 5199, 20256, 1556, 349, 305, 58, 152, 54, 123, 3],
  [2018, 15896, 6857, 22554, 1889, 410, 364, 64, 170, 59, 130, 3],
  [2019, 18593, 9396, 25560, 2309, 498, 432, 81, 192, 91, 144, 5],
  [2020, 21941, 12539, 28993, 2868, 619, 513, 117, 219, 129, 163, 6],
  [2021, 26702, 17317, 33582, 3691, 792, 640, 204, 263, 179, 188, 11],
  [2022, 29305, 19243, 35350, 4170, 890, 735, 257, 294, 205, 200, 12],
]

const sbaCityData = [
  ['year', 'Mountain View', 'Sunnyvale', 'Santa Cruz', 'San Mateo', 'Newark', 'Berkeley', 'Oakland', 'Menlo Park', 'Fairfax', 'San Francisco'],
  [1980, 42, 17, 10, 0, 38, 261, 15, 135, 24, 68],
  [1981, 46, 18, 13, 0, 44, 275, 15, 148, 27, 72],
  [1982, 51, 25, 15, 0, 47, 302, 18, 164, 29, 82],
  [1983, 59, 33, 20, 0, 54, 338, 22, 179, 30, 94],
  [1984, 67, 53, 23, 0, 62, 371, 26, 192, 32, 100],
  [1985, 82, 54, 27, 0, 66, 391, 26, 208, 40, 105],
  [1986, 101, 57, 33, 0, 71, 426, 28, 223, 44, 113],
  [1987, 124, 59, 44, 0, 81, 468, 35, 236, 55, 125],
  [1988, 147, 61, 53, 0, 102, 534, 43, 267, 77, 130],
  [1989, 195, 64, 65, 1, 131, 604, 52, 303, 108, 147],
  [1990, 238, 66, 77, 2, 172, 687, 59, 331, 140, 171],
  [1991, 288, 68, 97, 2, 208, 779, 66, 355, 177, 189],
  [1992, 347, 71, 106, 5, 266, 891, 79, 402, 211, 215],
  [1993, 403, 73, 124, 5, 305, 1004, 95, 446, 241, 239],
  [1994, 462, 75, 139, 8, 345, 1126, 102, 500, 275, 258],
  [1995, 496, 76, 153, 8, 383, 1236, 111, 525, 307, 283],
  [1996, 540, 77, 181, 8, 425, 1364, 112, 560, 349, 304],
  [1997, 589, 82, 205, 8, 460, 1513, 118, 600, 393, 327],
  [1998, 639, 85, 230, 8, 524, 1638, 129, 617, 442, 356],
  [1999, 687, 90, 250, 8, 586, 1817, 138, 637, 502, 389],
  [2000, 733, 98, 263, 8, 665, 1963, 146, 671, 558, 426],
  [2001, 772, 101, 281, 8, 725, 2117, 160, 693, 602, 454],
  [2002, 834, 107, 306, 9, 791, 2286, 172, 706, 656, 490],
  [2003, 912, 112, 334, 9, 890, 2492, 178, 734, 709, 524],
  [2004, 991, 113, 367, 11, 991, 2712, 199, 768, 785, 562],
  [2005, 1088, 121, 419, 14, 1097, 2927, 217, 812, 873, 610],
  [2006, 1204, 132, 463, 15, 1236, 3137, 243, 848, 959, 682],
  [2007, 1312, 151, 517, 15, 1338, 3377, 261, 908, 1032, 734],
  [2008, 1459, 166, 587, 18, 1461, 3665, 279, 953, 1130, 790],
  [2009, 1627, 185, 656, 23, 1586, 4009, 292, 1007, 1235, 858],
  [2010, 1810, 197, 715, 26, 1704, 4444, 309, 1048, 1354, 947],
  [2011, 2006, 205, 780, 29, 1829, 4806, 332, 1097, 1452, 1021],
  [2012, 2253, 240, 839, 32, 1983, 5172, 356, 1157, 1576, 1118],
  [2013, 2580, 274, 903, 35, 2143, 5574, 380, 1228, 1714, 1215],
  [2014, 2948, 316, 989, 38, 2325, 5988, 415, 1316, 1828, 1331],
  [2015, 3401, 381, 1076, 50, 2510, 6515, 456, 1402, 1976, 1457],
  [2016, 4016, 436, 1167, 58, 2713, 7094, 507, 1473, 2137, 1595],
  [2017, 4857, 466, 1274, 74, 2969, 7800, 579, 1569, 2316, 1743],
  [2018, 6138, 502, 1396, 109, 3237, 8743, 670, 1692, 2497, 1972],
  [2019, 7901, 561, 1540, 287, 3594, 10001, 770, 1827, 2750, 2275],
  [2020, 9927, 609, 1702, 601, 3961, 11328, 884, 1998, 3032, 2622],
  [2021, 11777, 661, 1886, 934, 4395, 12661, 1005, 2191, 3346, 3041],
  [2022, 12019, 676, 1940, 981, 4572, 12969, 1061, 2226, 3500, 3250],
]

// 根据数值调整国家上的点
const demoScale = (value: number) => {
  return scale(value, 0, 83879, 5, 60)
}

// 根据数值调整小地图上的点
const demoScaleMinMap = (value: number) => {
  return scale(value, 0, 35350, 10, 60)
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

