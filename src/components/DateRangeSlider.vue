<template>
  <div>
      <div class="tabs">
      <button @click="changeTab('years')" :class="{ 'active': activeTab === 'years' }">Years</button>
      <button @click="changeTab('months')" :class="{ 'active': activeTab === 'months' }">Months</button>
    </div>
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

        initializeMonthSlider() {
      const self = this;
      $("#slider-range-months").slider({
        range: true,
        min: new Date(self.selectedStartYear, 0).getTime(),
        max: new Date(self.selectedEndYear, 11).getTime(),
        values: [
          new Date(self.selectedStartYear, 0).getTime(),
          new Date(self.selectedEndYear, 11).getTime(),
        ],
        slide: function (event, ui) {
          const startMonth = new Date(ui.values[0]);
          const endMonth = new Date(ui.values[1]);
          const monthNames = [
            'Янв', 'Фев', 'Мар', 'Апр', 'Май', 'Июн', 'Июл',
            'Авг', 'Сен', 'Окт', 'Ноя', 'Дек',
          ];
          self.selectedStartMonth = `${monthNames[startMonth.getMonth()]} ${startMonth.getFullYear()}`;
          self.selectedEndMonth = `${monthNames[endMonth.getMonth()]} ${endMonth.getFullYear()}`;

          localStorage.setItem('selectedStartMonth', self.selectedStartMonth);
          localStorage.setItem('selectedEndMonth', self.selectedEndMonth);

          $(".start-tooltip").text(self.selectedStartMonth);
          $(".end-tooltip").text(self.selectedEndMonth);
        },
      });
    },
    changeTab(tab) {
      this.activeTab = tab;
      localStorage.setItem('activeTab', tab);
      this.updateScales();
      this.initializeSliders(); 
    },

    updateScales() {
      this.yearScale = Array.from({ length: this.selectedEndYear - this.selectedStartYear + 1 }, (_, index) => this.selectedStartYear + index);
      this.monthScale = this.generateMonthScale();
    },
    generateMonthScale() {
      const months = [];
      const totalMonths = (this.selectedEndYear - this.selectedStartYear + 1) * 12;
      const fontSizeFactor = totalMonths <= 36 ? 12 : (totalMonths <= 60 ? 10 : 8);

      for (let year = this.selectedStartYear; year <= this.selectedEndYear; year++) {
        months.push({ index: year, name: year, fontSize: fontSizeFactor });

        for (let month = 0; month < 12; month++) {
          const index = (year - this.selectedStartYear) * 12 + month;
          const fontSize = fontSizeFactor;
          months.push({ index, name: this.getMonthName(month), fontSize });
        }
      }
      return months;
    },


    getMonthName(index) {
      const monthNames = [
        'Янв', 'Фев', 'Мар', 'Апр', 'Май', 'Июн', 'Июл',
        'Авг', 'Сен', 'Окт', 'Ноя', 'Дек',
      ];
      return monthNames[index];
    },
  }
};
</script>

<style scoped>
.date-range-slider {
  width: 100%;
  max-width: 600px;
  margin: 20px auto;
}

.slider-wrapper {
  position: relative;
}

.slider {
  width: 100%;
}

.month-scale-item {
  flex: 1;
  text-align: center;
  font-size: 12px;
}

.tabs {
  display: flex;
  justify-content: center;
  margin-bottom: 50px;
  align-items: flex-start;
}
.tabs button {
  cursor: pointer;
  padding: 5px 10px;
  background-color: #eee;
  border: none;
  outline: none;
}

.tabs button.active {
  background-color: #ccc;
}

.months {
  display: flex;
  justify-content: space-between;
  margin: 15px 0 10px 0;
  flex-wrap: wrap; /* Добавлено для переноса на вторую строку при необходимости */
}

.months span {
  flex: 1;
  text-align: center;
}

.start-tooltip,
.end-tooltip {
  background-color: #2196f3;
  color: white;
  padding: 5px 10px;
  border-radius: 5px;
}

.slider-container {
  position: relative;
}

.tooltip {
  position: absolute;
  background-color: #2196f3;
  color: white;
  padding: 5px 10px;
  border-radius: 5px;
  white-space: nowrap;
  text-align: center;
  bottom: 70px;
}

.end-tooltip {
  right: 0;
}

.month-scale {
  display: flex;
  justify-content: space-between;
  padding-top: 15px; 
  flex-wrap: wrap; /* Добавлено для переноса на вторую строку при необходимости */
}

.month-scale-item {
  flex: 1;
  text-align: center;
  border-left: 1px solid #ccc; 
  border-right: 1px solid #ccc;
  margin-bottom: 5px; /* Добавлено для отступа между элементами */
}

.year-scale {
  display: flex;
  justify-content: space-between;
  padding-top: 25px;
}

.year-scale-item {
  flex: 1;
  text-align: center;
  font-size: 12px;
  border-left: 1px solid #ccc; 
  border-right: 1px solid #ccc; 
}

/* Адаптивные стили */
@media only screen and (max-width: 1100px) {
  .year-scale-item, .month-scale-item {
    flex: 0 0 calc(25% - 10px); /* Четыре элемента в строке с отступами между ними */
    margin-bottom: 10px; /* Добавлено для отступа между строками */
  }
}

@media only screen and (max-width: 600px) {
  .tabs {
    flex-direction: column;
    align-items: center;
  }

  .tabs button {
    margin-bottom: 10px;
  }

  .year-scale, .month-scale {
    flex-wrap: wrap;
  }

  .year-scale-item, .month-scale-item {
    flex: 0 0 100%;
  }
}
</style>