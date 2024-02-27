<template>
  <div>
    <div v-if="activeTab === 'years'">
      <div class="slider-container">
        <div id="slider-range-years"></div>
        <div class="year-scale">
          <div v-for="year in yearScale" :key="year" class="year-scale-item">{{ year }}</div>
        </div>
        <div class="tooltip start-tooltip" v-show="activeTab === 'years'">{{ selectedStartYear }}</div>
        <div class="tooltip end-tooltip" v-show="activeTab === 'years'">{{ selectedEndYear }}</div>
      </div>

    </div>
    <div v-else>
      <div id="slider-range-months" class="slider-container">

        <div class="month-scale">
          <div v-for="month in monthScale" :key="month.index" class="month-scale-item" :style="{ fontSize: month.fontSize + 'px' }">{{ month.name }}</div>
        </div>
        <div class="tooltip start-tooltip" v-show="activeTab === 'months'">{{ selectedStartMonth }}</div>
        <div class="tooltip end-tooltip" v-show="activeTab === 'months'">{{ selectedEndMonth }}</div>
      </div>

    </div>
    <div class="tabs">
      <button @click="changeTab('years')" :class="{ 'active': activeTab === 'years' }">Years</button>
      <button @click="changeTab('months')" :class="{ 'active': activeTab === 'months' }">Months</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {

    };
  },



};
</script>

<style scoped>


</style>