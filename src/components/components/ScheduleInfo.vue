<template>
  <li
    class="informatin_row"
    :key="`info_${item.schedule_id}`"
    :index="index"
    :style="getInformationSize"
  >
    <div class="information_col">
      <input type="checkbox" @change="onChange" />
    </div>
    <div class="information_col">{{ item.company_displayname }}</div>
    <div class="information_col">{{ item.location_name }}</div>
  </li>
</template>
<script>
export default {
  data: () => {
    return {
      checked: false,
    };
  },
  mounted() {
    this.$watch(
      () => this.forchChecked,
      (newValue, oldValue) => {
        if (newValue !== oldValue) {
          this.checked = newValue;
        }
      },
      {
        immediatge: true,
      }
    );
  },
  props: {
    item: { type: Object },
    index: { type: Number },
    forchChecked: { type: Boolean },
    cellRect: { type: Object },
  },
  computed: {
    // informationの高さ
    getInformationSize() {
      return `height:${this.cellRect.height}px;line-height::${this.cellRect.height}px;`;
    },
  },
  methods: {
    onChange($event) {
      this.checked = $event.target.checked;
      this.$emit("onChange", {
        schedule_id: this.item.schedule_id,
        checked: $event.target.checked,
      });
    },
  },
};
</script>