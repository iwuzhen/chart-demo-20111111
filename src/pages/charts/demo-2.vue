<script setup lang="ts">
import * as echarts from 'echarts'
import 'echarts/extension/bmap/bmap'

import { onMounted } from 'vue'

import * as d3 from 'd3-geo'
import 'd3-array'

import { geoCoordMap } from '~/hook/utils'
// import * as d3 from "d3-geo-projection";

const USAprojection = d3.geoAlbersUsa()
// const projection = d3.geoEqualEarth()

const data = [
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

const dataUSA = [
  {
    name: '纽约',
    value: 112,
  },
]

const convertData = function (data) {
  const res = []
  for (let i = 0; i < data.length; i++) {
    const geoCoord = geoCoordMap[data[i].name]
    if (geoCoord) {
      res.push({
        name: data[i].name,
        value: geoCoord.concat(data[i].value),
      })
    }
  }
  return res
}

const main = async (chartObj) => {
  let response = await fetch('/data/usa.json')
  echarts.registerMap('USA', await response.json())

  response = await fetch('/data/100000_full.json')
  echarts.registerMap('China', await response.json())

  const option = {
    title: {
      text: 'China and US',
    },
    tooltip: {
      trigger: 'item',
      showDelay: 0,
      transitionDuration: 0.2,
    },
    toolbox: {
      show: true,
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
        name: 'China',
        type: 'map',
        map: 'China',
        left: 20,
        zoom: 1,
        roam: false,
        // projection: {
        //     project: (point) => [point[0] / 180 * Math.PI, -Math.log(Math.tan((Math.PI / 2 + point[1] / 180 * Math.PI) / 2))],
        //     unproject: (point) => [point[0] * 180 / Math.PI, 2 * 180 / Math.PI * Math.atan(Math.exp(point[1])) - 90]
        // },
        // projection: {
        //   project: function (point) {
        //     return projection(point);
        //   },
        //   unproject: function (point) {
        //     return projection.invert(point);
        //   },
        // },
        geoIndex: 0,
        itemStyle: {
          areaColor: '#eee',
          color: 'rgba(255,255,255,244)',
          borderColor: '#fff',
        },
        center: [100, 30],
        emphasis: {
          disabled: true,
          label: {
            show: false,
          },
        },
      },
      {
        geoIndex: 1,
        name: 'USA',
        type: 'map',
        map: 'USA',
        right: 40,
        projection: {
          project(point) {
            return USAprojection(point)
          },
          unproject(point) {
            return USAprojection.invert(point)
          },
        },
        center: ['0%', '40%'],
        // layoutCenter: ['30%', '30%'],
        // layoutSize: 500,
        itemStyle: {
          areaColor: '#eee',
          color: 'rgba(255,255,255,244)',
          borderColor: '#fff',
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
        name: 'China',
        map: 'China',
        type: 'effectScatter',
        coordinateSystem: 'geo',
        geoIndex: 0,
        data: convertData(
          data
            .sort((a, b) => {
              return b.value - a.value
            })
            .slice(0, 6),
        ),
        encode: {
          value: 2,
        },
        symbolSize(val) {
          return val[2] / 10
        },
        showEffectOn: 'emphasis',
        rippleEffect: {
          brushType: 'stroke',
        },
        hoverAnimation: true,
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
        symbolSize(val) {
          return val[2] / 10
        },
        showEffectOn: 'emphasis',
        rippleEffect: {
          brushType: 'stroke',
        },
        hoverAnimation: true,
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
  console.log(option)
  chartObj.setOption(option)
}

// import { Scene, PointLayer } from '@antv/l7';
// import { GaodeMap, Mapbox } from '@antv/l7-maps';

// echarts.registerMap('USA', chinaGeo, {
//   Alaska: {
//     // 把阿拉斯加移到美国主大陆左下方
//     left: -131,
//     top: 25,
//     width: 15
//   },
//   Hawaii: {
//     left: -110,
//     top: 28,
//     width: 5
//   },
//   'Puerto Rico': {
//     // 波多黎各
//     left: -76,
//     top: 26,
//     width: 2
//   }
// });

onMounted(async () => {
  const elem = document.getElementById('chartID')
  if (!elem)
    return
  // let chartObj = echarts.init(elem);
  const chartObj = echarts.init(elem, undefined, { renderer: 'svg' })

  main(chartObj)
  // const scene = new Scene({
  //   id: 'map',
  //   map: new Mapbox({
  //     style: 'light',
  //     pitch: 0,
  //     center: [113.6873837, 22.6037922],
  //     zoom:  5.32,
  //     token: "pk.eyJ1IjoiaWRlciIsImEiOiJjaW1laWU3NXIwMTA5dmdtNmd3c2N1M3E4In0.i1Y6ezP5n0VlkUjUvGR_RA",
  //   }),
  // });
  // fetch(
  // 'https://gw.alipayobjects.com/os/basement_prod/d3564b06-670f-46ea-8edb-842f7010a7c6.json'
  // )
  // .then(res => res.json())
  // .then(data => {
  //   const pointLayer = new PointLayer()
  //     .source(data)
  //     .shape('circle')
  //     .size('mag', [1, 25])
  //     .color('mag', ['#5B8FF9', '#5CCEA1'])
  //     .style({
  //       opacity: 0.3,
  //       strokeWidth: 1,
  //     });

  //   scene.addLayer(pointLayer);
  // })
})
</script>

<template>
  <div id="chartID" style="width: 100vw;height:100vh" />
</template>

<route lang="yaml">
meta:
  layout: empty
</route>

