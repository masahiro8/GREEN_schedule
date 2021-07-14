<template>
  <div id="app">
    <ScheduleRow
      v-for="item in schedule.devices"
      :key="item.device_id"
      :item="item"
      :today="schedule.date"
    />
  </div>
</template>

<script>
import ScheduleRow from "./components/ScheduleRow";
import response_device from "@/json/device_response.json";

export default {
  name: "App",
  data: () => {
    return {
      schedule: { ...response_device.contents.schedule },
    };
  },
  mounted() {
    console.log("mounted", this.schedule.date);
  },
  components: {
    ScheduleRow,
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
