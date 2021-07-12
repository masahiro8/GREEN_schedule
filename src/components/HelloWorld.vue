<template>
  <div class="schedule">
    <div class="scheduleInner" :style="getDaySize">
      <ScheduleTimeLabel
        :cellRectWidth="cellRect.width"
        :totalTime="total_hours"
        :prevTime="prev_hours"
        :afterTime="after_hours"
        :dayDivide="day_divide"
      />
      <div class="scheduleBackground" :style="getDaySize">
        <div
          v-for="(item, index) in cells"
          :key="`cell_${index}`"
          class="scheduleCell"
          :class="`${getCellStatus(index)} cell_${index}`"
          :style="getCellStyle"
        ></div>
        <div
          v-for="(item, index) in getHorizontalLines"
          :key="`line_${index}`"
          class="scheduleHorizontalLine"
          :style="getHorizontalLineTop(index)"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
import ScheduleTimeLabel from "./components/ScheduleTimeLabel";
/**
 * 前後 12時間を加えて、全部で48時間の幅を持つ
 */

//1時間の分割数
const config_day_divide = 4;

//当日前の時間
const config_schedule_prev_hours = 12;
//当日後の時間
const config_schedule_after_hours = 12;
//スケジュールで表示する時間
const config_schedule_hours =
  24 + config_schedule_prev_hours + config_schedule_after_hours;

//スケジュールで表示する15分セルの数
const config_all_cells = config_day_divide * config_schedule_hours;

//1スケジュールの高さ
const config_cell_height = 32;
const config_cell_width = 42;

//作業時間帯仮データ
// const work_timeframe = { startTime: "12:00", endTime: "18:00" };

//今日
// const today = "2021-01-01";

export default {
  components: {
    ScheduleTimeLabel,
  },
  data: () => {
    return {
      plans: [
        {
          id: 1,
          type: 1,
          startTime: "2021-01-01 12:30:00",
          endTime: "2021-01-01 13:30:00",
        },
        {
          id: 2,
          type: 1,
          startTime: "2021-01-01 14:30:00",
          endTime: "2021-01-01 15:30:00",
        },
        {
          id: 3,
          type: 1,
          startTime: "2021-01-01 20:30:00",
          endTime: "2021-01-01 20:30:00",
        },
        {
          id: 4,
          type: 1,
          startTime: "2021-01-01 20:30:00",
          endTime: "2021-01-01 20:30:00",
        },
      ],
      cells: config_all_cells,
      total_hours: config_schedule_hours,
      prev_hours: config_schedule_prev_hours,
      after_hours: config_schedule_after_hours,
      cellRect: { width: config_cell_width, height: config_cell_height },
      day_divide: config_day_divide,
    };
  },
  computed: {
    getDaySize() {
      return `width: ${config_all_cells * 24}px;height:${
        this.plans.length * config_cell_height + 24
      }px`;
    },
    getHorizontalLines() {
      return [...new Array(this.plans.length)];
    },
    getCellStyle() {
      return `width:${this.cellRect.width}px;height:${
        this.cellRect.height * this.plans.length
      }px;`;
    },
  },
  mounted() {},
  methods: {
    getHorizontalLineTop(index) {
      const top = index * config_cell_height;
      return `top:${top}px`;
    },

    getCellStatus(index) {
      const isPrev = index < config_schedule_prev_hours * config_day_divide;
      const isAfter =
        index >=
        config_all_cells - config_schedule_prev_hours * config_day_divide;

      if (isPrev) return "isPrev";
      if (isAfter) return "isAfter";
      return null;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.schedule {
  overflow-x: scroll;
  overflow-y: hidden;
  width: 50%;
}

.scheduleInner {
  position: relative;
  height: 100%;
}

//背景
.scheduleBackground {
  position: relative;
  height: 100%;
  display: flex;
}
.scheduleCell {
  position: relative;
  height: 100%;
  background-color: #f8f8f8;
  &:nth-of-type(4n) {
    &:before {
      content: "";
      position: absolute;
      z-index: 0;
      width: 1px;
      height: 100%;
      top: 0;
      right: 0;
      background-color: #ddd;
    }
  }

  &.isPrev,
  &.isAfter {
    background-color: #e8e8e8;
    /* &:before {
      display: none;
    } */
  }
}
.scheduleHorizontalLine {
  position: absolute;
  height: 1px;
  width: 100%;
  left: 0;
  background-color: #cfcfcf;
}
</style>