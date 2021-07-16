<template>
  <div class="">
    <div class="table">
      <ul class="information">
        <!-- ラベル -->
        <div class="schedule__label">
          <div class="schedule__type-label">
            <div>
              <span class="type-circle"></span
              ><span class="type-label">占有</span>
            </div>
            <div>
              <span class="type-circle"></span
              ><span class="type-label">スポット</span>
            </div>
            <div>
              <span class="type-circle"></span
              ><span class="type-label">禁止</span>
            </div>
            <div>
              <span class="type-circle"></span
              ><span class="type-label">重複</span>
            </div>
          </div>
        </div>
        <!-- TODO:サマリー -->
        <div class="schedule__summary">
          <button @click="toggleChild">tgl</button>
          <input type="checkbox" @change="onChangeForceChecked" />
        </div>
        <!-- 予定情報 -->
        <div v-if="isShowChild" class="schedule__plan">
          <ScheduleInfo
            v-for="(item, index) in getPlans"
            :key="`info_${item.schedule_id}`"
            :item="item"
            :index="index"
            :cellRect="cellRect"
            :forchChecked="forchChecked"
            @onChange="onCheck"
          />
        </div>
      </ul>
      <div class="schedule">
        <div class="scheduleInner" :style="getDaySize">
          <!-- 時間表記 -->
          <div class="schedule__label">
            <ScheduleTimeLabel
              :timelabelHeight="margin_top"
              :cellRectWidth="cellRect.width"
              :totalTime="total_hours"
              :prevTime="prev_hours"
              :afterTime="after_hours"
              :hourDivide="hour_divide"
            />
          </div>
          <!-- TODO:サマリー -->
          <div class="schedule__summary">
            <ScheduleSummaryCell
              v-for="(item, index) in getSummary"
              :key="`summary_${index}`"
              :index="index"
              :item="item"
              :cellRect="summaryRect"
              :hourDivide="hour_divide"
              :today="getToday"
              :todayStartX="getTodayStartX"
              :marginTop="margin_top"
            ></ScheduleSummaryCell>
          </div>
          <!-- 予定 -->
          <div v-if="isShowChild" class="schedule__plan">
            <SchedulePlanCell
              v-for="(item, index) in getPlans"
              :key="`plan_${item.schedule_id}`"
              :index="index"
              :item="item"
              :cellRect="cellRect"
              :hourDivide="hour_divide"
              :today="getToday"
              :todayStartX="getTodayStartX"
              :marginTop="margin_top"
            ></SchedulePlanCell>
          </div>
          <!-- 背景 -->
          <div
            v-if="isShowChild"
            class="scheduleBackground"
            :style="getDaySize"
          >
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
    <div class="table_footer" v-if="isShowChild">
      <button>load more</button>
    </div>
  </div>
</template>

<script>
import ScheduleTimeLabel from "./components/ScheduleTimeLabel";
import SchedulePlanCell from "./components/SchedulePlanCell";
import ScheduleSummaryCell from "./components/ScheduleSummaryCell";
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

//予定の上マージン
const config_margin_top = 0;

//さらに表示する高さ
const config_loadmore_height = 64;

//作業時間帯仮データ
// const work_timeframe = { startTime: "12:00", endTime: "18:00" };

//今日
// const today = "2021-01-01";

export default {
  components: {
    ScheduleTimeLabel,
    SchedulePlanCell,
    ScheduleSummaryCell,
    ScheduleInfo,
  },
  data: () => {
    return {
      checkList: [],
      forchChecked: false,
      isShowChild: true,
      cells: config_all_cells,
      total_hours: config_schedule_hours,
      prev_hours: config_schedule_prev_hours,
      after_hours: config_schedule_after_hours,
      summaryRect: { width: config_cell_width, height: 1 },
      cellRect: { width: config_cell_width, height: config_cell_height },
      hour_divide: config_hour_divide,
      margin_top: config_margin_top,
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
    getToday() {
      return `${this.today} 00:00:00`;
    },
    getSummary() {
      const { schedule_summary } = this.item;
      return schedule_summary;
    },
    // 子要素一覧
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
      const height = this.isShowChild
        ? (children.length + 2) * config_cell_height + config_margin_top
        : config_cell_height + config_margin_top;

      return `width: ${
        config_all_cells * config_cell_width
      }px;height:${height}px`;
    },
    //スケジュールの予定量にあわせて罫線を作成
    getHorizontalLines() {
      const { children } = this.item;
      return [...new Array(children.length + 1)];
    },
    //スケジュールのグリッドのスタイルを作成
    getCellStyle() {
      const { children } = this.item;
      return `width:${this.cellRect.width}px;height:${
        this.cellRect.height * children.length + config_margin_top
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
    onChangeForceChecked($event) {
      this.forchChecked = $event.target.checked;
      console.log($event.target.checked);
    },
    // チェックリストを更新
    onCheck({ schedule_id, checked }) {
      let checkList = [...this.checkList];
      //trueの場合
      if (checked) {
        const result = checkList.find((id) => id === schedule_id);
        if (!result) checkList.push(schedule_id);
      } else {
        checkList = checkList.filter((id) => {
          return id !== schedule_id;
        });
      }
      this.checkList = checkList;
      console.log("__", schedule_id, checkList);
    },
    getHorizontalLineTop(index) {
      const top = index * config_cell_height + config_margin_top;
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
    toggleChild() {
      this.isShowChild = !this.isShowChild;
      console.log("toggleChild", this.isShowChild);
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

.table_footer {
  height: 36px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.information {
  position: relative;
  margin: 0;
  margin-left: 2px;
  padding: 0;
  width: 50%;
  background-color: white;

  .informatin_row {
    display: flex;
    justify-content: flex-start;
    text-align: left;
    font-size: 11px;
    border-left: 1px solid #efefef;
    border-bottom: 1px solid #efefef;
  }

  .schedule__plan {
    margin-left: 4px;
  }
}
.schedule {
  overflow-x: scroll;
  overflow-y: hidden;
  width: 100%;
  background-color: white;
}

.scheduleInner {
  position: relative;
  height: 100%;
}

.schedule__label {
  height: 24px;
}
.schedule__type-label {
  display: flex;
  align-items: center;
  .type-circle {
    display: inline-block;
    width: 10px;
    height: 10px;
    background-color: black;
    border-radius: 5px;
    margin-right: 2px;
  }
  .type-label {
    font-size: 10px;
    height: 10px;
    margin-right: 8px;
  }
}

.schedule__summary {
  position: relative;
  height: 37px;
  top: 0;
}

.schedule__plan {
  position: relative;
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