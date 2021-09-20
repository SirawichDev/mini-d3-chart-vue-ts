<template>
  <div>
    <h1>Mini Chart eieie</h1>
    <AreaChart :width="500" :height="300" :chartData="data" />
    <!-- <div class="content">
      <div>
        <label for="itemCount">Record Count: </label>
        <input id="itemCount" v-model.number="itemCount" />
      </div>
      <div>
        <label for="min">Min Value: </label>
        <input id="min" v-model.number="min" />
      </div>
      <div>
        <label for="max">Max Value: </label>
        <input id="max" v-model.number="max" />
      </div>
    </div> -->
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { interval, now } from "d3-timer";
import AreaChart from "@/components/AreaChart.vue";
const REFRESH_RATE = 2000;
function generateData(size: number, min: number, max: number): Array<number> {
  console.log(
    "random",
    Array.from(
      { length: size },
      () => Math.floor(Math.random() * (max - min + 1)) + min
    )
  );
  return Array.from(
    { length: size },
    () => Math.floor(Math.random() * (max - min + 1)) + min
  );
}
export default Vue.extend({
  name: "Demo",
  data() {
    return {
      data: [] as Array<number>,
      itemCount: 25 as number,
      min: 10 as number,
      max: 100 as number,
    };
  },
  components: {
    AreaChart,
  },
  mounted() {
    interval(
      () => {
        this.data = generateData(this.itemCount, this.min, this.max);
      },
      REFRESH_RATE,
      now() - REFRESH_RATE
    );
  },
});
</script>

<style scoped>
label {
  display: inline-block;
  width: 150px;
}
</style>
