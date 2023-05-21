<template>
  <div id="app">
    <PieChart class="pie" :data="rawData" />
  </div>
</template>

<script>
import PieChart from "./components/PieChart.vue";

export default {
  components: {
    PieChart,
  },
  data: () => ({
    rawData: [
      {
        Male: {
          "18-24": 433,
          "25-34": 1367,
          "35-44": 100,
          "45-54": 2650,
          "55-64": 141,
          "65+": 1086,
        },
        Female: {
          "18-24": 597,
          "25-34": 1482,
          "35-44": 1957,
          "45-54": 2653,
          "55-64": 1719,
          "65+": 1601,
        },
      },
      {
        Male: {
          "18-24": 2930,
          "25-34": 12939,
          "35-44": 23299,
          "45-54": 2323,
          "55-64": 12389,
          "65+": 945,
        },
        Female: {
          "18-24": 2930,
          "25-34": 69990,
          "35-44": 49000,
          "45-54": 40030,
          "55-64": 30030,
          "65+": 1304,
        },
      },
    ],
  }),
};
</script>

<style scoped>
.pie .piechart .male:hover + .bar {
  display: block;
}
</style>