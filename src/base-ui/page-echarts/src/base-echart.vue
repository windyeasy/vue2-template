<template>
  <div class="echart" ref="chartRef" :style="{ height: echartHeight }"></div>
</template>
<script>
  import * as echarts from "echarts";
  export default {
    props: {
      option: {
        type: Object,
        default: () => ({}),
      },
      height: {
        type: [Number, String],
        default: 300,
      },
    },
    data() {
      return {
        echartsInstance: {},
      };
    },
    computed: {
      echartHeight() {
        const height =
          typeof this.height === "number" ? this.height + "px" : props.height;
        return height;
      },
    },
    watch: {
      option() {
        if (this.echartsInstance) {
          this.echartsInstance.setOption(this.option);
        }
      },
    },
    mounted() {
      const echartEl = this.$refs.chartRef;
      const echartsInstance = echarts.init(echartEl, "light", {
        renderer: "canvas",
      });
      this.echartsInstance = echartsInstance;
      echartsInstance.setOption(this.option);
      window.addEventListener("resize", () => {
        echartsInstance.resize();
      });
    },
  };
</script>
