<template>
  <div class="schedulePlan" :style="style"></div>
</template>
<script>
import { differenceInMinutes } from "date-fns";
export default {
  data: () => {
    return {
      style: "",
    };
  },
  props: {
    item: {
      type: Object,
    },
    index: {
      type: Number,
    },
    //1時間の分割数
    hourDivide: {
      type: Number,
    },
    //cellRect.widthが1時間の幅ピクセル
    cellRect: {
      type: Object,
    },
    //今日の日付
    today: {
      type: String,
    },
    //今日の開始ピクセル
    todayStartX: {
      type: Number,
    },
    //上部のマージン
    marginTop: {
      type: Number,
    },
  },
  mounted() {
    this.$watch(
      () => this.item,
      (newValue) => {
        const { start_time, end_time } = newValue;

        const today = new Date(this.today);
        const start = new Date(start_time);
        const end = new Date(end_time);

        const rect = {
          x: null,
          width: null,
        };

        // 継続時間
        const diffMinContinuous = differenceInMinutes(end, start);
        rect.width =
          (diffMinContinuous * (this.cellRect.width * this.hourDivide)) / 60;

        // 開始位置
        const diffMinStart = differenceInMinutes(start, today);
        console.log("--", start, today, this.todayStartX, diffMinStart);
        rect.x =
          this.todayStartX +
          diffMinStart * ((this.cellRect.width * this.hourDivide) / 60);

        rect.top = this.index * 2 * this.cellRect.height + this.marginTop;
        rect.height = this.cellRect.height;

        this.style = `width:${rect?.width}px;height:${rect?.height}px;left:${rect?.x}px;top:${rect?.top}px;`;

        // console.log(this.todayStartX, rect);
      },
      {
        immediate: true,
        deep: true,
      }
    );
  },
};
</script>
<style lang="scss" scoped>
.schedulePlan {
  position: absolute;
  background-color: red;
  z-index: 1;
  border-radius: 4px;
}
</style>