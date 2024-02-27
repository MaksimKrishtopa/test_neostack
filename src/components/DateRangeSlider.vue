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
      activeTab: localStorage.getItem('activeTab') || 'years',
      selectedStartYear: parseInt(localStorage.getItem('selectedStartYear')) || 2014,
      selectedEndYear: parseInt(localStorage.getItem('selectedEndYear')) || 2021,
      selectedMonth: '',
      yearScale: [],
      monthScale: [],
      selectedStartMonth: localStorage.getItem('selectedStartMonth') || '',
      selectedEndMonth: localStorage.getItem('selectedEndMonth') || '',
    };
  },
  watch: {
    activeTab: 'initializeSliders',
  },
  mounted() {
    
    if (!localStorage.getItem('activeTab')) {
      localStorage.setItem('activeTab', 'years');
    }
    this.activeTab = localStorage.getItem('activeTab') || 'years';

    this.updateScales();
    this.initializeSliders();
  },
  methods: {
    initializeSliders() {
      this.$nextTick(() => {
        if (this.activeTab === 'years') {
          this.initializeYearSlider();
        } else {
          this.initializeMonthSlider();
        }
      });
    },
    initializeYearSlider() {
      const self = this;

      $("#slider-range-years").slider({
        range: true,
        min: 2014,
        max: 2021,
        values: [self.selectedStartYear, self.selectedEndYear],
        step: 1/12, 
        slide: function (event, ui) {
          self.selectedStartYear = ui.values[0];
          self.selectedEndYear = ui.values[1];

          localStorage.setItem('selectedStartYear', self.selectedStartYear);
          localStorage.setItem('selectedEndYear', self.selectedEndYear);
        },
        change: function (event, ui) {
          
          $(".start-tooltip").text(`${self.formatYearMonth(ui.values[0])}`);
          $(".end-tooltip").text(`${self.formatYearMonth(ui.values[1])}`);
        },
      });
    },
        formatYearMonth(value) {
      const year = Math.floor(value);
      const month = Math.round((value - year) * 12);
      return `${year} ${this.getMonthName(month)}`;
    },
  }
};
</script>

<style scoped>


</style>