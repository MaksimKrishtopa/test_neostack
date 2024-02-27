<template>
  <div>
    <h1>Date Range Slider</h1>
    <DateRangeSlider
      :minYear="minYear"
      :maxYear="maxYear"
      @updateRange="handleRangeUpdate"
    />
  </div>
</template>

<script>
import DateRangeSlider from "@/components/DateRangeSlider.vue";

export default {
  components: {
    DateRangeSlider,
  },
  data() {
    return {
      minYear: 2014,
      maxYear: 2021,
    };
  },

    methods: {
    handleRangeUpdate({ startYear, endYear }) {
      console.log(`Selected range: ${startYear} - ${endYear}`);
     },
  },

};
</script>
