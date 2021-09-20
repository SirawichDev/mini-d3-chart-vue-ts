<template>
  <svg :width="width" :height="height">
    <defs>
      <linearGradient id="header-shape-gradient" x2="0.35" y2="1">
        <stop offset="0%" stop-color="var(--color-stop)" />
        <stop offset="30%" stop-color="var(--color-stop)" />
        <stop offset="100%" stop-color="var(--color-bot)" />
      </linearGradient>
    </defs>
    <g>
      <path class="area" :d="areaPath(this.data)" />
      <path class="line" :d="linePath(this.data)" />
    </g>
  </svg>
</template>

<script lang="ts">
import Vue from "vue";
import { axisBottom, axisLeft } from "d3-axis";
import { easeCubic } from "d3-ease";
import { interpolate } from "d3-interpolate";
import { ScaleLinear, scaleLinear } from "d3-scale";
import { select } from "d3-selection";
import { area, line, curveLinear, Area, Line } from "d3-shape";
import { timer } from "d3-timer";

const MARGIN = 25;
const ANIMATION_MS = 1000;
export default Vue.extend({
  name: "AreaChart",
  props: {
    chartData: {
      type: Array,
      default: () => [] as Array<number>,
    },
    width: {
      type: Number,
      required: true,
    },
    height: {
      type: Number,
      required: true,
    }
  },
  data() {
    return {
      data: this.chartData.slice() as Array<number>
    };
  },
  created() {
    console.log("this", this.$refs);
  },
  computed: {
    scaleX(): ScaleLinear<number, number> {
      return scaleLinear()
        .domain([0, this.data.length - 1])
        .range([MARGIN, this.width - MARGIN]);
    },
    scaleY(): ScaleLinear<number, number> | any {
      return scaleLinear()
        .domain([0, this.data.length ? Math.max.apply(this, this.data) : 0])
        .range([this.height - MARGIN, MARGIN]);
    },
    areaPath(): Area<[number, number]> {
      return area().x((d, i) => this.scaleX(i));
    },
    linePath(): Line<[number, number]> {
      return line()
        .x((d, i) => this.scaleX(i))
        .y((d) => this.scaleY(d))
        .curve(curveLinear);
    },
  },
  watch: {
    chartData(newData, oldData): void {
      const interpolator = interpolate(oldData, newData);
      const t = timer((elapsed) => {
        this.data = interpolator(easeCubic(elapsed / ANIMATION_MS)).slice();
        if (elapsed > ANIMATION_MS) {
          t.stop();
        }
      });
    },
    scaleX(): void {
      select((this as any).$refs.axisBottom).call(
        axisBottom(this.scaleX).scale(this.scaleX)
      );
    },
    scaleY(): void {
      select((this as any).$refs.axisLeft).call(
        axisLeft(this.scaleY).scale(this.scaleY)
      );
    },
  },
});
</script>

<style scoped>
#header-shape-gradient {
  --color-stop: #f12c06;
  --color-bot: #faed34;
}
.area {
  fill: #3c118b !important;
}
.line {
  stroke: #f7685b;
  stroke-width: 1px;
  /* fill: url(#header-shape-gradient) #fff; */
  fill: none;
}
</style>
