<template>
  <div class="timeLabels">
    <div
      v-for="time in prevLabel"
      :key="`prev_${time}`"
      class="timeLabel"
      :style="cellStyle"
    >
      {{ time }}
    </div>
    <div
      v-for="time in mainLabel"
      :key="`main_${time}`"
      class="timeLabel"
      :style="cellStyle"
    >
      {{ time }}
    </div>
    <div
      v-for="time in afterLabel"
      :key="`after_${time}`"
      class="timeLabel"
      :style="cellStyle"
    >
      {{ time }}
    </div>
  </div>
</template>
<script>
export default {
  props: {
    totalTime: {
      type: Number,
    },
    prevTime: {
      type: Number,
    },
    afterTime: {
      type: Number,
    },
    dayDivide: {
      type: Number,
    },
    cellRectWidth: {
      type: Number,
      default: 0,
    },
  },
  computed: {
    // 1セルの分
    cellMin() {
      return Math.floor(60 / this.dayDivide);
    },
    cellStyle() {
      return `width:${this.cellRectWidth * this.dayDivide}px`;
    },
    mainLabel() {
      let time = 0;
      const label = [...new Array(24)].map((item) => {
        return time++;
      });
      return label;
    },
    prevLabel() {
      let time = 24 - this.prevTime;
      const label = [...new Array(this.prevTime)].map((item) => {
        return time++;
      });
      return label;
    },
    afterLabel() {
      let time = 0;
      const label = [...new Array(this.afterTime)].map((item) => {
        return time++;
      });
      return label;
    },
  },
};
</script>
<style lang="scss" scoped>
.timeLabels {
  display: flex;
  justify-content: space-between;
}
.timeLabel {
  font-size: 11px;
  text-align: center;
}
</style>