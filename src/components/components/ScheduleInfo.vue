<template>
  <li
    class="informatin_row"
    :key="`info_${item.schedule_id}`"
    :index="index"
    :style="getInformationSize"
  >
    <div class="information_col"></div>
    <div class="information_col">{{ item.company_displayname }}</div>
    <div class="information_col">{{ item.location_name }}</div>
  </li>
</template>
<script>
export default {
  props: {
    item: { type: Object },
    index: { type: Number },
    cellRect: { type: Object },
  },
  computed: {
    // informationの高さ
    getInformationSize() {
      return `height:${this.cellRect.height}px`;
    },
  },
};
</script>