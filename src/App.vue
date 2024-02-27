<template>
  <div id="app">
    <DateRangeSliderView />
  </div>
</template>

<script>
import DateRangeSliderView from "@/views/DateRangeSliderView.vue";

export default {
  components: {
    DateRangeSliderView,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background-color: #e0e0e0;
  padding: 50px;
}

h1 {
  color: #2196f3;
}
</style>
