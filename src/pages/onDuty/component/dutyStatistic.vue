<template>
  <vis-border title="监管概况" width="100%" height="35.2rem">
    <div class="supervision-statistic">
      <div class="panel-content">
        <div class="panel-container">
          <div class="image-box">
            <dash-board showImage="true"></dash-board>
          </div>

          <div class="panel-value">
            <span class="panel-title">综合指数</span>
            <span class="panel-percent">{{ percent }}</span>
            <span class="panel-level">良好</span>
          </div>
        </div>

        <div class="chart" ref="myChart"></div>
      </div>
    </div>
  </vis-border>
</template>

<script>
import { get } from "utils/http";
import DashBoard from "common/dash-board";
import echarts from "echarts";
import { mapState } from "vuex";
import VisBorder from "common/back-fram";

export default {
  name: "page",
  components: {
    VisBorder,
    DashBoard,
  },
  data() {
    return {
      myChart: null,
      option: null,
      percent: 65,

      labels: [],
      values: [],
    };
  },
  mounted() {
    this.getData();
  },
  computed: {
    ...mapState(["fontSize"]),
  },
  methods: {
    getData() {
      get(`/api/car/supervision`).then((res) => {
        const { percent, list } = res.data;
        this.percent = percent;
        list.forEach(({ name, value }) => {
          this.labels.push({
            name: name + "&&" + value,
            min: 0,
          });
          this.values.push({ value });
        });

        this.initChart();
      });
    },
    initChart() {
      const { fontSize, labels, values } = this;
      this.option = {
        radar: [
          {
            radius: "60%",
            name: {
              textStyle: {
                rich: {
                  a: {
                    fontSize: 1.6 * fontSize,
                    color: "rgba(250,250,250,1)",
                  },
                  b: {
                    fontSize: 1.6 * fontSize,
                    color: "#2598FF",
                    padding: [0, 0, 0, 10],
                  },
                },
              },
              formatter: function (params) {
                console.log(params);
                const [name, value] = params.split("&&");
                return `{a|${name}}` + `{b|${value}}`;
              },
            },
            indicator: labels,
            center: ["50%", "50%"], // 位置
            shape: "circle", //形状
            splitArea: {
              areaStyle: {
                color: "transparent", //圆环颜色
                shadowColor: "aqua", // 圆颜色
                shadowBlur: 10,
              },
            },
            axisLine: {
              lineStyle: {
                color: "#08585F", // 分割线
              },
            },
            splitLine: {
              lineStyle: {
                color: "rgba(255,255,255,0.4)", //圆线
                width: 0.5,
              },
            },
          },
        ],
        series: [
          {
            type: "radar",
            data: [
              {
                value: [72, 8.76, 72.11, 6.09],
                itemStyle: {
                  opacity: 0,
                },
                lineStyle: {
                  width: 1,
                  color: "rgba(255,255,255,0.4)",
                },
                areaStyle: {
                  normal: {
                    color: {
                      type: "radial",
                      x: 0.5,
                      y: 0.5,
                      r: 0.5,
                      colorStops: [
                        {
                          offset: 0,
                          color: "rgba(50, 123, 250, 0.7)", // 0% 处的颜色
                        },
                        {
                          offset: 1,
                          color: "rgba(50, 123, 250, 0.5)", // 100% 处的颜色
                        },
                      ],
                      global: false, // 缺省为 false
                    },
                  },
                },
              },
            ],
          },
        ],
      };

      this.myChart = echarts.init(this.$refs.myChart);
      this.myChart.setOption(this.option);
    },
  },
};
</script>
<style lang="scss">
.supervision-statistic {
  height: calc(100% - 4.2rem);
  .panel-content {
    height: 100%;
    position: relative;
    display: flex;
    align-items: center;
    > div {
      flex: 1;
    }

    .image-box {
      width: 25rem;
      height: 21.3rem;
      .value {
        color: #f1b129;
        font-size: 8.5rem;
      }
      .label {
        font-size: 2.4rem;
      }
    }

    .chart {
      width: 40rem;
      height: 28rem;
    }
  }
  .panel-container {
    display: flex;
    align-items: center;
    padding-left: 5rem;
  }
}
.panel-value {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: "AlibabaPuHuiTi-Medium";
  margin-left: 5rem;
  .panel-title {
    font-size: 2.4rem;
    font-weight: 500;
    color: #fff;
  }
  .panel-percent {
    font-size: 7.5rem;
    font-weight: bold;
    color: #44c4d8;
    line-height: 8.7rem;
    margin: 0.5rem auto;

    font-family: "DINAlternate-Bold, DINAlternate";
  }
  .panel-level {
    width: 4.8rem;
    height: 2.4rem;
    line-height: 2.4rem;
    border-radius: 0.2rem;
    border: 0.1rem solid #979797;
    color: #fff;
    text-align: center;
  }
}
</style>
