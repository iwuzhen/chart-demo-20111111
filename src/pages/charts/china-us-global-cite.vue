<script setup lang="ts">
import * as echarts from 'echarts'
import _ from 'lodash'
import { onMounted } from 'vue'
import 'd3-array'
// import { scaleLinear, scaleSqrt } from 'd3-scale'
import { scaleSqrt } from 'd3-scale'
import { geoCoordMap } from '~/hook/utils'

const xScale = scaleSqrt()
  .domain([0, 4000])
  .range([0, 1])

const usaColors = ['rgb(82, 5, 245, 1)', 'rgb(43, 51, 252, 1)', 'rgb(28, 106, 230, 1)', ' rgb(43, 196, 252, 1)', 'rgb(39, 245, 228,1)', 'rgb(82, 23, 215, 1)']
const zhColors = ['rgb(242,12,32,1)', 'rgb(252,85,13,1)', 'rgb(230,141,0,1)']

const usArea = ['gb_area', 'gh_area', 'san_bay', 'rt_area', 'da_area', 'New York']
const zhArea = ['zh', 'Shanghai', 'Beijing']

const getMMcolor = (obj: { value: any; data: { source: string; coords: number[][] } }) => {
  // const usaColors = ['rgb(82, 5, 245)', 'rgb(43, 51, 252)', 'rgb(28, 106, 230)', ' rgb(43, 196, 252)', 'rgb(39, 245, 228)']
  // console.log(obj.value)
  const alpha = String(xScale(obj.value))
  if (usArea.includes(obj.data.source)) {
    const color = usaColors[usArea.indexOf(obj.data.source)]
    return color.replace(/[^,]+(?=\))/, alpha)
  }

  if (zhArea.includes(obj.data.source)) {
    const color = zhColors[zhArea.indexOf(obj.data.source)]
    return color.replace(/[^,]+(?=\))/, alpha)
  }
  // return `rgb(82,5,245,${alpha})`

  if (obj.data.coords[0][0] > 180)
    return `rgb(48,11,218,${alpha})`
  return `rgb(194,43,10,${alpha})`
}

const chartData = [['year', '旧金山湾区', '北卡三角研究区', '大波士顿', '达拉斯大都会区', '休斯顿大都会区', '粤港澳湾区', '纽约', '上海', '北京'], [1980, 4850, 739, 10846, 2746, 2547, 15, 3985, 3, 15], [1981, 5636, 849, 12206, 3172, 3003, 24, 4432, 3, 18], [1982, 6685, 986, 13510, 3534, 3392, 28, 4792, 3, 23], [1983, 7852, 1196, 15430, 3941, 3799, 35, 5304, 3, 28], [1984, 9044, 1404, 17065, 4412, 4260, 50, 5977, 7, 37], [1985, 10307, 1591, 19145, 4809, 4837, 72, 6568, 9, 55], [1986, 12092, 1799, 21743, 5288, 5507, 97, 7346, 9, 80], [1987, 14037, 2038, 24593, 5943, 6338, 118, 8079, 10, 116], [1988, 16755, 2409, 28801, 6632, 7486, 142, 8989, 11, 165], [1989, 20070, 2832, 34322, 7760, 8903, 179, 10160, 18, 220], [1990, 24027, 3555, 40933, 8906, 10649, 245, 11516, 24, 321], [1991, 28248, 4335, 48032, 10287, 12702, 323, 13078, 37, 420], [1992, 33332, 5409, 56803, 11917, 15151, 427, 14927, 63, 515], [1993, 38669, 6521, 65454, 13841, 17643, 572, 17025, 90, 656], [1994, 45999, 7945, 75597, 16039, 20577, 770, 19773, 111, 826], [1995, 52958, 9463, 85524, 18227, 23621, 1021, 22270, 148, 1017], [1996, 61020, 11276, 96368, 20844, 27056, 1463, 25263, 199, 1237], [1997, 69498, 13186, 107801, 23791, 30645, 2092, 28466, 243, 1488], [1998, 79001, 15323, 120496, 27177, 34554, 2869, 32073, 296, 1790], [1999, 89382, 17805, 134719, 31191, 39335, 3877, 36016, 376, 2128], [2000, 101052, 20511, 150591, 35790, 44504, 5221, 40574, 488, 2648], [2001, 113969, 23479, 167207, 40660, 49891, 6915, 45612, 639, 3294], [2002, 128664, 26853, 186024, 46235, 56434, 9131, 51558, 827, 4325], [2003, 146406, 30958, 208295, 52688, 64235, 11986, 58608, 1054, 5901], [2004, 167539, 35658, 233180, 60282, 73372, 16080, 66984, 1465, 8345], [2005, 192697, 40973, 262439, 69655, 84564, 21088, 76936, 2142, 11905], [2006, 223136, 47339, 297520, 80809, 98147, 28135, 88655, 3430, 17214], [2007, 258055, 54659, 337870, 94080, 114760, 37052, 101782, 5356, 24881], [2008, 297597, 62920, 383348, 109517, 133646, 48938, 116691, 8655, 36220], [2009, 343965, 72518, 434282, 127337, 154726, 63698, 133881, 13406, 51416], [2010, 396598, 82962, 490669, 147453, 178754, 81578, 153110, 19535, 70550], [2011, 454462, 94818, 552626, 169660, 205863, 102522, 174454, 27142, 93601], [2012, 518132, 107945, 618735, 194801, 234859, 126948, 197729, 36330, 121172], [2013, 591694, 123614, 694681, 223864, 268595, 156978, 224890, 48317, 157471], [2014, 675824, 141631, 779946, 256671, 306914, 194853, 255731, 63277, 204374], [2015, 777813, 162323, 876357, 293748, 349659, 240039, 292324, 81793, 263358], [2016, 898796, 184906, 983029, 334079, 396253, 296204, 335068, 104552, 337826], [2017, 1044857, 211477, 1101873, 377477, 447087, 366290, 384853, 132666, 429568], [2018, 1210404, 238837, 1225967, 420708, 497149, 444959, 439646, 165147, 534863], [2019, 1458649, 275636, 1393478, 476692, 562327, 567374, 513562, 215310, 698724], [2020, 1715652, 314631, 1571710, 533692, 628837, 704916, 593320, 273843, 881919], [2021, 2036771, 364692, 1798986, 606709, 715604, 914196, 693673, 363956, 1156536], [2022, 2165662, 392701, 1921747, 645864, 764267, 1048038, 747786, 427952, 1335697]]

onMounted(async () => {
  let response = await fetch('/data/world_r.json')
  echarts.registerMap('world', await response.json())

  response = await fetch('/data/coordinate_area.json')
  const areaCoordinate = await response.json()

  response = await fetch('/data/coordinate_city.json')
  const cityCoordinate = await response.json()

  const elem = document.getElementById('chartID')
  // let chartObj = echarts.init(elem);
  if (!elem)
    return

  const chartObj = echarts.init(elem, undefined, { renderer: 'canvas' })

  // init ret by year data
  const unlimateLineData: any[] = []
  const unlimateSactterData: any[] = []
  const beginYear = 1980
  const lastYear = 2022

  const timeData = []
  for (let year = beginYear; year <= lastYear; year++) {
    unlimateLineData.push([])
    unlimateSactterData.push([])
    timeData.push(year)
  }

  for (const row of areaCoordinate) {
    const sourceLL = geoCoordMap[row[1]]
    const yearIndex = row[0] - beginYear
    // if (sourceLL[0] < -10) {
    //   unlimateSactterData[yearIndex].push(
    //     {
    //       color: 'red',
    //       value: [row[5] < -10 ? 360 + row[5] : row[5], row[4]],
    //     },
    //   )
    // }
    // else {
    //   unlimateSactterData[yearIndex].push(
    //     {
    //       color: 'blue',
    //       value: [row[5] < -10 ? 360 + row[5] : row[5], row[4]],
    //     },
    //   )
    // }
    let coords
    const x1 = sourceLL[0] < -10 ? 360 + sourceLL[0] : sourceLL[0]
    const x2 = row[5] < -10 ? 360 + row[5] : row[5]
    const y1 = sourceLL[1]
    const y2 = row[4]
    if (usArea.includes(row[1])) {
      if (x1 < x2)
        coords = [[x1, y1], [x2, y2]]
      else
        coords = [[x2, y2], [x1, y1]]
    }
    else {
      if (x1 < x2)
        coords = [[x2, y2], [x1, y1]]
      else
        coords = [[x1, y1], [x2, y2]]
    }

    unlimateLineData[yearIndex].push(
      {
        value: row[3],
        source: row[1],
        dest: row[2],
        // coords: [[120.19, 30.26], row],
        coords,
      },
    )
  }

  for (const row of cityCoordinate) {
    const yearIndex = row[0] - beginYear
    let coords
    const x1 = row[5] < -10 ? 360 + row[5] : row[5]
    const x2 = row[7] < -10 ? 360 + row[7] : row[7]
    const y1 = row[4]
    const y2 = row[6]
    if (usArea.includes(row[1])) {
      if (x1 < x2)
        coords = [[x1, y1], [x2, y2]]
      else
        coords = [[x2, y2], [x1, y1]]
    }
    else {
      if (x1 < x2)
        coords = [[x2, y2], [x1, y1]]
      else
        coords = [[x1, y1], [x2, y2]]
    }

    unlimateLineData[yearIndex].push(
      {
        value: row[3],
        source: row[1],
        dest: row[2],
        // coords: [[120.19, 30.26], row],
        coords,
      },
    )
  }

  // console.log(lineData)
  const option: echarts.EChartsOption = {
    backgroundColor: 'rgb(0,131,255,0.2)',

    grid: [
      {
        show: false,
        id: 0,
        right: 440,
        bottom: 60,
        height: 200,
        width: 330,
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
          // color: 'rgb(255,255,255,0.8)',
        },
        splitLine: {
          show: true,
          lineStyle: {
            opacity: 0.2,
          },
        },
      },
    ],
    timeline: {
      show: true,
      axisType: 'category',
      autoPlay: false,
      loop: false,
      playInterval: 3000,
      data: timeData,
      currentIndex: 0,
      right: 900,
      left: 10,
    },
    tooltip: {
      show: false,
      trigger: 'none',
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
        id: 0,
        name: 'world',
        type: 'map',
        map: 'world',
        projection: {
          project: point => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
          unproject: point => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90],
        },
        zoom: 1.4,
        emphasis: {
          disabled: true,
        },
        // center: [10, 30],
        top: -50,
        left: 300,
        regions: [{
          name: 'China',
          itemStyle: {
            areaColor: 'rgb(255,0,0,0.2)',
          },
        }, {
          name: 'United States',
          itemStyle: {
            areaColor: 'rgb(0,255,0,0.2)',
          },
        }],
      },
    ],
    options: unlimateLineData.map((lineData, index) => {
      let sortCache: any[] = []
      chartData[0].slice(1).forEach((v, subIndex) => {
        sortCache.push([v, chartData[index].slice(1)[subIndex]])
      })
      sortCache = sortCache.sort((a, b) => b[1] - a[1])
      return {
        title: [{
          text: `${chartData[index + 1][0]}`,
          left: 500,
          bottom: 240,
          textStyle: {
            // color: 'rgb(254,254,254,0.7)',
            fontSize: 58,
          },
        }, {
          text: '论文引用分布',
          left: 1,
          top: 1,
          textStyle: {
            color: 'rgb(254,254,254,0.9)',
            fontSize: 20,
          },
        }],
        legend: {
          show: true,
          orient: 'vertical',
          data: sortCache.map(item => item[0]),
          // z: 30,
          right: 280,
          bottom: 50,
          textStyle: {
            // color: 'rgb(255,255,255)',
            fontSize: 16,
          },
        },
        series: [
          ...chartData[0].slice(1).map((cityName, cityIndex): echarts.SeriesOption => {
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
              data: chartData.slice(1).map((dataItem, currentIndex) => {
                if (currentIndex <= index)
                  return [dataItem[0], dataItem[cityIndex + 1]]
                else
                  return [dataItem[0], '-']
              }),
            }
          }),
          {
            name: 'bgLine',
            coordinateSystem: 'geo',
            type: 'lines',
            symbol: 'circle',
            symbolSize: 6,
            // polyline: true,
            lineStyle: {
              color: (param: any) => getMMcolor(param),
              // width: param => getSize(param),
              width: 1,
              // opacity: 0.5,
              curveness: 0.4,
            },
            label: {
              show: false,
            },
            silent: true,
            animation: false,
            large: false,
            cap: 'round',
            clip: false,
            data: lineData,
          },
          // ...lineData.map((obj) => {
          //   // console.log(obj)
          //   return {
          //     name: 'bgLine',
          //     cap: 'round',
          //     coordinateSystem: 'geo',
          //     type: 'lines',
          //     animation: false,
          //     lineStyle: {
          //       color: param => getMMcolor(param),
          //       width: getSize(obj.value),
          //       // width: obj.value,
          //       // width: 2,
          //       opacity: 0.5,
          //       curveness: 0.2,
          //     },
          //     clip: false,
          //     data: [obj],
          //   }
          // }),
          // {
          //   name: 'scatter',
          //   type: 'scatter',
          //   coordinateSystem: 'geo',
          //   // rippleEffect: {
          //   //   scale: 5,
          //   //   brushType: 'stroke',
          //   // },
          //   label: {
          //     show: false,
          //     position: 'right',
          //     formatter: '{b}',
          //   },
          //   largeThreshold: 20,
          //   animation: false,
          //   silent: true,
          //   symbolSize: 5,
          //   itemStyle: {
          //     // color: param => getColor(param.data.value[2]),
          //     color: param => param.data.color,
          //   },
          //   data: unlimateSactterData[index],
          // },
          // {
          //   name: 'sLine',
          //   type: 'lines',
          //   coordinateSystem: 'geo',
          //   effect: {
          //     show: true,
          //     period: 6,
          //     trailLength: 0.6,
          //     symbolSize: [10, 20],
          //   },
          //   lineStyle: {
          //     color: param => getMMcolor(param),
          //     width: param => getSize(param),
          //     curveness: 0.2,
          //   },
          //   data: lineData,
          // },
        ],
      } as echarts.EChartsOption
    }),

    animationDuration: 0,
  }
  // console.log(option)
  // console.log(option)
  setTimeout(() => {
    chartObj.setOption(option)
  }, 1000)

  window.onresize = _.debounce(() => {
    chartObj.resize()
  }, 500)
})
</script>

<template>
  <div>
    <!-- <script src="" /> -->
    <div id="chartID" class="echart-container" style="width: 1920px;height:1080px" />
  </div>

  <!-- <div id="chartID" class="echart-container" style="width: 600px;height:600px;border-radius: 50%;" /> -->
</template>

<route lang="yaml">
meta:
  layout: empty
</route>

