<template>
  <div class="table">
    <ul class="information">
      <div class="schedule__label">label</div>
      <!-- TODO:サマリー -->
      <!-- 予定情報 -->
      <div class="schedule__plan">
        <ScheduleInfo
          v-for="(item, index) in getPlans"
          :key="`info_${item.id}`"
          :item="item"
          :index="index"
          cellRect="cellRect"
        />
      </div>
    </ul>
    <div class="schedule">
      <div class="scheduleInner" :style="getDaySize">
        <!-- 時間表記 -->
        <ScheduleTimeLabel
          :timelabelHeight="timelabel_height"
          :cellRectWidth="cellRect.width"
          :totalTime="total_hours"
          :prevTime="prev_hours"
          :afterTime="after_hours"
          :hourDivide="hour_divide"
        />
        <!-- TODO:サマリー -->
        <!-- 予定 -->
        <div class="schedule__plan">
          <SchedulePlanCell
            v-for="(item, index) in getPlans"
            :key="`plan_${item.id}`"
            :index="index"
            :item="item"
            :cellRect="cellRect"
            :hourDivide="hour_divide"
            :today="today"
            :todayStartX="getTodayStartX"
            :marginTop="timelabel_height"
          ></SchedulePlanCell>
        </div>
        <div class="scheduleBackground" :style="getDaySize">
          <!-- 背景グリッド -->
          <div
            v-for="(item, index) in cells"
            :key="`cell_${index}`"
            class="scheduleCell"
            :class="`${getCellStatus(index)} cell_${index}`"
            :style="getCellStyle"
          ></div>
          <!-- 罫線 -->
          <div
            v-for="(item, index) in getHorizontalLines"
            :key="`line_${index}`"
            class="scheduleHorizontalLine"
            :style="getHorizontalLineTop(index)"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ScheduleTimeLabel from "./components/ScheduleTimeLabel";
import SchedulePlanCell from "./components/SchedulePlanCell";
import ScheduleInfo from "./components/ScheduleInfo";

/**
 * 前後 12時間を加えて、全部で48時間の幅を持つ
 */

//1時間の分割数
const config_hour_divide = 4;

//当日前の時間
const config_schedule_prev_hours = 12;
//当日後の時間
const config_schedule_after_hours = 12;
//スケジュールで表示する時間
const config_schedule_hours =
  24 + config_schedule_prev_hours + config_schedule_after_hours;

//スケジュールで表示する15分セルの数
const config_all_cells = config_hour_divide * config_schedule_hours;

//1時間のセルのサイズ(px)
const config_cell_width = 8;
const config_cell_height = 32;

const config_timelabel_height = 15;

//作業時間帯仮データ
// const work_timeframe = { startTime: "12:00", endTime: "18:00" };

//今日
// const today = "2021-01-01";

export default {
  components: {
    ScheduleTimeLabel,
    SchedulePlanCell,
    ScheduleInfo,
  },
  data: () => {
    return {
      cells: config_all_cells,
      total_hours: config_schedule_hours,
      prev_hours: config_schedule_prev_hours,
      after_hours: config_schedule_after_hours,
      cellRect: { width: config_cell_width, height: config_cell_height },
      hour_divide: config_hour_divide,
      timelabel_height: config_timelabel_height,
    };
  },
  props: {
    item: {
      type: Object,
    },
    today: {
      type: String,
    },
  },
  computed: {
    getPlans() {
      const { children } = this.item;
      return children;
    },

    // informationの高さ
    getInformationSize() {
      return `height:${config_cell_height}px`;
    },
    //スケジュールの大きさを表示する時間範囲によって変更
    getDaySize() {
      const { children } = this.item;
      return `width: ${config_all_cells * config_cell_width}px;height:${
        (children.length + 1) * config_cell_height + config_timelabel_height
      }px`;
    },
    //スケジュールの予定量にあわせて罫線を作成
    getHorizontalLines() {
      const { children } = this.item;
      return [...new Array(children.length)];
    },
    //スケジュールのグリッドのスタイルを作成
    getCellStyle() {
      const { children } = this.item;
      return `width:${this.cellRect.width}px;height:${
        this.cellRect.height * children.length
      }px;`;
    },
    //今日の開始ピクセル位置
    getTodayStartX() {
      const prevWidth =
        config_schedule_prev_hours * this.cellRect.width * config_hour_divide;
      return prevWidth;
    },
  },
  mounted() {},
  methods: {
    getHorizontalLineTop(index) {
      const top = index * config_cell_height;
      return `top:${top}px`;
    },

    getCellStatus(index) {
      const isPrev = index < config_schedule_prev_hours * config_hour_divide;
      const isAfter =
        index >=
        config_all_cells - config_schedule_prev_hours * config_hour_divide;

      if (isPrev) return "isPrev";
      if (isAfter) return "isAfter";
      return null;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.table {
  width: 100%;
  height: auto;
  display: flex;
}
.information {
  margin: 0;
  padding: 0;
  width: 50%;

  .informatin_row {
    display: flex;
    justify-content: flex-start;
    text-align: left;
    font-size: 11px;
  }
}
.schedule {
  overflow-x: scroll;
  overflow-y: hidden;
  width: 100%;
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