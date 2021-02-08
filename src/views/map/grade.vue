<template>
  <div class="common-main com-box-main">
    <div class="com-box-title">预报模式</div>
    <div class="distribute-chart">
      <echarts
        id="distributeCharts"
        :chart-style="chartStyle"
        :option="option"
        @getChartInstance="getInstance"
      />
    </div>
  </div>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      chartStyle: {
        width: '100%',
        height: '100%'
      },
      option: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            // 坐标轴指示器，坐标轴触发有效
            type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          left: '20%',
          top: '8%',
          right: '2%',
          bottom: '8%'
        },
        xAxis: {
          interval: 10,
          position: 'top',
          type: 'value',
          splitLine: {
            show: false
          },
          axisLine: {
            lineStyle: {
              color: '#ffffff',
              opacity: 0.3
            }
          },
          axisTick: {
            show: true,
            lineStyle: {
              color: '#ffffff',
              opacity: 0.3
            }
          },
          axisLabel: {
            color: '#fff',
            fontSize: 12,
            fontWeight: 400
          }
        },
        yAxis: {
          type: 'category',
          data: [
            'ave', 'best', 'double', 'polynomial', 'EC'
          ],
          inverse: true,
          axisTick: {
            show: true
          },
          axisLine: {
            show: false
          },
          splitLine: {
            show: false
          },
          axisLabel: {
            color: '#fff',
            fontSize: 14,
            fontWeight: 400,
            align: 'left',
            margin: 73
          }
        },
        series: [
          {
            selectedMode: 'single',
            select: {
              label: {
                show: true
              },
              itemStyle: {
                color: '#645aa5'
              }
            },
            data: [88, 95, 75, 64, 53],
            type: 'bar',
            barWidth: '13',
            itemStyle: {
              color: {
                type: 'linear',
                x: 0,
                y: 0,
                x2: 1,
                y2: 0,
                colorStops: [
                  {
                    offset: 0,
                    color: '#82ffc2' // 0% 处的颜色
                  },
                  {
                    offset: 1,
                    color: '#2c84a3' // 100% 处的颜色
                  }
                ],
                globalCoord: false // 缺省为 false
              }
            },
            emphasis: {
              itemStyle: {
                color: {
                  type: 'linear',
                  x: 0,
                  y: 0,
                  x2: 1,
                  y2: 0,
                  colorStops: [
                    {
                      offset: 0,
                      color: '#82ffc2' // 0% 处的颜色
                    },
                    {
                      offset: 1,
                      color: '#2c84a3' // 100% 处的颜色
                    }
                  ],
                  globalCoord: false // 缺省为 false
                }
              }
            },
            markLine: {
              lineStyle: {
                type: 'dashed',
                color: '#fff',
                opacity: 0.2
              },
              symbol: ['none', 'none'],
              data: []
            }
          }
        ]
      },
      instance: '',
    }
  },
  watch: {
  },
  created() {
    // this.setData(this.distributeData)
  },
  methods: {
    getInstance(instance) {
      this.instance = instance
      console.log('in', instance);
      instance.on('selectchanged', (params) => {
        console.log('变化', params);
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.com-box-title {
  margin: 4px 30px;
  font-size: 18px;
  color: #fff;
}
.distribute-chart {
  width: 100%;
  height: 100%;
}
</style>
