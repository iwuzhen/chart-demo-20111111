<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import * as d3 from 'd3-geo'
import 'd3-array'
import { convertData, scale } from '~/hook/utils'
import mapBackground from '~/assert/mapBackground.png'
import { geoMapItemStyle, publicConfig } from '~/hook/sharedata'

const USAprojection = d3.geoAlbersUsa()

const demoData = [['year', '粤港澳湾区', '旧金山湾区', '上海', '纽约'], [1980, 19, 835, 0, 645], [1981, 23, 906, 2, 681], [1982, 27, 1008, 2, 720], [1983, 38, 1129, 6, 782], [1984, 45, 1258, 10, 845], [1985, 57, 1375, 14, 921], [1986, 67, 1530, 18, 995], [1987, 73, 1730, 28, 1086], [1988, 93, 2010, 50, 1217], [1989, 121, 2376, 65, 1374], [1990, 155, 2751, 81, 1517], [1991, 204, 3146, 105, 1684], [1992, 255, 3640, 138, 1824], [1993, 338, 4145, 173, 1985], [1994, 514, 4714, 203, 2163], [1995, 714, 5196, 231, 2335], [1996, 1013, 5747, 289, 2503], [1997, 1337, 6386, 343, 2685], [1998, 1742, 6974, 419, 2893], [1999, 2137, 7630, 502, 3083], [2000, 2570, 8293, 657, 3334], [2001, 3124, 8945, 843, 3572], [2002, 3768, 9689, 1132, 3854], [2003, 4513, 10561, 1516, 4204], [2004, 5532, 11492, 2217, 4636], [2005, 6758, 12550, 3185, 5072], [2006, 8378, 13682, 4453, 5578], [2007, 10164, 14844, 5932, 6115], [2008, 12381, 16232, 7928, 6652], [2009, 14876, 17777, 10222, 7224], [2010, 17718, 19509, 12675, 7946], [2011, 20463, 21190, 14983, 8644], [2012, 23233, 23097, 17194, 9421], [2013, 25986, 25198, 19415, 10312], [2014, 29067, 27436, 21685, 11312], [2015, 32102, 30055, 23861, 12428], [2016, 35650, 32976, 26089, 13604], [2017, 40076, 36537, 28719, 14952], [2018, 45890, 41115, 32185, 16690], [2019, 53927, 47693, 37128, 18942], [2020, 63671, 54443, 42999, 21582], [2021, 77458, 61545, 51185, 24512], [2022, 83879, 63614, 55464, 25570]]

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
  ['year', 'Mountain View', 'Newark', 'Berkeley', 'Menlo Park', 'Fairfax', 'Palo Alto', 'Albany', 'San Jose', 'San Francisco', 'Santa Clara'],
  [1980, 42, 38, 261, 135, 24, 46, 79, 18, 68, 10],
  [1981, 46, 44, 275, 148, 27, 53, 91, 19, 72, 11],
  [1982, 51, 47, 302, 164, 29, 63, 102, 19, 82, 14],
  [1983, 59, 54, 338, 179, 30, 77, 110, 20, 94, 16],
  [1984, 67, 62, 371, 192, 32, 95, 118, 21, 100, 17],
  [1985, 82, 66, 391, 208, 40, 121, 128, 23, 105, 20],
  [1986, 101, 71, 426, 223, 44, 151, 142, 26, 113, 24],
  [1987, 124, 81, 468, 236, 55, 180, 155, 41, 125, 26],
  [1988, 147, 102, 534, 267, 77, 204, 189, 57, 130, 32],
  [1989, 195, 131, 604, 303, 108, 231, 217, 82, 147, 39],
  [1990, 238, 172, 687, 331, 140, 261, 247, 96, 171, 45],
  [1991, 288, 208, 779, 355, 177, 284, 285, 113, 189, 53],
  [1992, 347, 266, 891, 402, 211, 319, 337, 129, 215, 69],
  [1993, 403, 305, 1004, 446, 241, 372, 384, 152, 239, 80],
  [1994, 462, 345, 1126, 500, 275, 442, 425, 196, 258, 96],
  [1995, 496, 383, 1236, 525, 307, 501, 482, 226, 283, 111],
  [1996, 540, 425, 1364, 560, 349, 564, 534, 261, 304, 131],
  [1997, 589, 460, 1513, 600, 393, 670, 589, 293, 327, 154],
  [1998, 639, 524, 1638, 617, 442, 742, 628, 330, 356, 183],
  [1999, 687, 586, 1817, 637, 502, 813, 674, 367, 389, 215],
  [2000, 733, 665, 1963, 671, 558, 879, 711, 405, 426, 246],
  [2001, 772, 725, 2117, 693, 602, 969, 751, 447, 454, 279],
  [2002, 834, 791, 2286, 706, 656, 1050, 807, 499, 490, 324],
  [2003, 912, 890, 2492, 734, 709, 1145, 867, 556, 524, 371],
  [2004, 991, 991, 2712, 768, 785, 1241, 924, 592, 562, 414],
  [2005, 1088, 1097, 2927, 812, 873, 1339, 980, 640, 610, 488],
  [2006, 1204, 1236, 3137, 848, 959, 1427, 1041, 710, 682, 563],
  [2007, 1312, 1338, 3377, 908, 1032, 1538, 1104, 771, 734, 632],
  [2008, 1459, 1461, 3665, 953, 1130, 1659, 1171, 855, 790, 709],
  [2009, 1627, 1586, 4009, 1007, 1235, 1843, 1209, 968, 858, 805],
  [2010, 1810, 1704, 4444, 1048, 1354, 2052, 1268, 1088, 947, 917],
  [2011, 2006, 1829, 4806, 1097, 1452, 2237, 1339, 1207, 1021, 1048],
  [2012, 2253, 1983, 5172, 1157, 1576, 2459, 1410, 1355, 1118, 1179],
  [2013, 2580, 2143, 5574, 1228, 1714, 2668, 1474, 1554, 1215, 1308],
  [2014, 2948, 2325, 5988, 1316, 1828, 2860, 1570, 1749, 1331, 1449],
  [2015, 3401, 2510, 6515, 1402, 1976, 3001, 1671, 2024, 1457, 1634],
  [2016, 4016, 2713, 7094, 1473, 2137, 3123, 1787, 2319, 1595, 1835],
  [2017, 4857, 2969, 7800, 1569, 2316, 3223, 1897, 2726, 1743, 2095],
  [2018, 6138, 3237, 8743, 1692, 2497, 3362, 2040, 3164, 1972, 2389],
  [2019, 7901, 3594, 10001, 1827, 2750, 3584, 2212, 4012, 2275, 2826],
  [2020, 9927, 3961, 11328, 1998, 3032, 3694, 2370, 4613, 2622, 3237],
  [2021, 11777, 4395, 12661, 2191, 3346, 3812, 2549, 5310, 3041, 3690],
  [2022, 12019, 4572, 12969, 2226, 3500, 3870, 2626, 5517, 3250, 3855],
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
    let sortCache: any[] = []
    demoData[0].slice(1).forEach((v, index) => {
      sortCache.push([v, item.slice(1)[index]])
    })

    sortCache = sortCache.sort((a, b) => b[1] - a[1])
    // console.log(, item, index)
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
        text: '论文',
        left: 1,
        top: 1,
        textStyle: {
          color: 'rgb(254,254,254,0.3)',
          fontSize: 20,
        },
      }],
      legend: {
        show: true,
        orient: 'vertical',
        data: sortCache.map(item => item[0]),
        // z: 30,
        right: 550,
        bottom: 200,
        textStyle: {
          color: 'rgb(255,255,255)',
          fontSize: 16,
        },
      },
      series: [
        ...demoData[0].slice(1).map((cityName, cityIndex): echarts.SeriesOption => {
          return {
            id: cityName,
            name: cityName,
            type: 'line',
            xAxisIndex: 0,
            yAxisIndex: 0,
            showSymbol: false,
            // endLabel: {
            //   show: true,
            //   fontSize: 20,
            //   formatter(params: any) {
            //     // console.log(params)
            //     return `${params.seriesId}: ${params.value[1]}`
            //   },
            // },
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
            // color: 'rgb(139,183,255)',
            color: 'yellow',
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
            borderColor: 'rgb(238,72,28)',
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
          labelLayout(params: any): any {
            const ret = {
              y: params.labelRect.y,
              x: params.labelRect.x,
              // x: params.rect.x,
              // moveOverlap: 'shiftX',
              align: 'left',
              verticalAlign: 'middle',
            }
            if (params.text === 'Albany')
              ret.y -= 10
            if (params.text === 'Berkeley') {
              ret.align = 'right'
              ret.y += 20
              ret.x -= 40
            }
            if (params.text === 'San Francisco')
              ret.y += 20
            if (params.text === 'Newark')
              ret.y -= 20
            if (params.text === 'Menlo Park') {
              // ret.y -= 20
              ret.x -= 20
              ret.align = 'right'
            }
            if (params.text === 'Palo Alto') {
              ret.y += 20
              ret.x -= 20
              ret.align = 'right'
            }
            if (params.text === 'Santa Clara') {
              ret.y += 30
              ret.x -= 20
              ret.align = 'right'
            }

            if (params.text === 'Santa ClaraSan Jose') {
              ret.y += 30
              ret.x -= 20
              ret.align = 'right'
            }
            return ret
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
            // color: 'rgb(255, 255, 255)',
            position: 'right',
            show: true,
          },
          itemStyle: {
            // color: 'rgb(0, 47, 146)',
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
        splitNumber: 2,
        axisLabel: {
          color: 'rgb(255,255,255,0.8)',
        },
        splitLine: {
          show: true,
          lineStyle: {
            opacity: 0.2,
          },
        },
        // max: 100000,
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
        layoutCenter: [1398, 269],
        layoutSize: 443,

        itemStyle: {
          areaColor: {
            imageWidth: 1920,
            imageHeight: 1080,
            image: mapBackground,
            repeat: 'no-repeat',
          },
        },
      },
    ],
    series: [
      {
        id: 'ChinaPoint',
        type: 'scatter',
        coordinateSystem: 'geo',
        geoIndex: 0,
        data: [{
          name: '粤港澳湾区',
          value: [113.541389, 22.195833],
        }],
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
    color: ['#ee6666', '#fac858', '#91cc75', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#5470c6', '#ea7ccc'],
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
              x1: 1150,
              y1: 463,
              x2: 1219,
              y2: 419,
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
      // replaceMerge: 'legend',
      autoPlay: false,
      loop: false,
      playInterval: publicConfig.playInterval,
      data: demoData.slice(1).map(item => item[0]),
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

