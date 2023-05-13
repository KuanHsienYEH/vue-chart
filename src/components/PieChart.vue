<template>
  <div class="piechart-svg-container" align="center">
    <div class="chart_warpper">
      <div ref="chartContainer" class="piechart">
        <BarChart class="bar" 
        :style="{ left: barChartPosition.x + 'px', top: barChartPosition.y + 'px' }" 
        v-if="showBarChart" 
        :data="barChartData" />
      </div>
    </div>
    <div class="btn_wrapper">
      <button
        v-for="(item, index) in data"
        :key="index"
        :class="currentDataset === item ? 'focus' : ''"
        @click="updatePie(index)"
      >
        Dataset {{ index + 1 }}
      </button>
    </div>
  </div>
</template>

<script>
import * as d3 from "d3";
import BarChart from "./BarChart.vue";

export default{
  components: {
    BarChart,
  },
  props: {
    data: Array,
  },
  data: () => ({
    currentDataset: [],
    currentFocusSex: "",
    total: 0,
    SEX: {
      MALE: "Male",
      FEMALE: "Female",
    },
    populationBySex: [],
    barData:{},
    barChartPosition: { x: 0, y: 0 },
    showBarChart: false,

  }),
  beforeMount() {
    this.currentDataset = this.data[0];
  },
  mounted() {
    this.defaultPie();
  },
  methods: {
    sortingPopulationBySex(data) {
      this.populationBySex = [];
      for (let item in data) {
        const distribution = Object.values(data[item]);
        const index = this.populationBySex.length;
        const obj = {
          sex: item,
          population: distribution.reduce((acc, cur) => acc + cur),
          distribution
        };
        this.populationBySex.splice(index, 0, obj);
      }
    },
    drawPie(data) {
      const width = 350,
        height = 350,
        radius = Math.min(width, height) / 2,
        color = d3.scaleOrdinal(d3.schemeCategory10);

      const arc = d3
        .arc()
        .innerRadius(radius - 100)
        .outerRadius(radius - 10);

      const pie = d3
        .pie()
        .sort(null)
        .value((d) => d.population);

      const chartContainer = this.$refs.chartContainer; 
      const svg = d3
        .select(chartContainer)
        .append("svg")
        .attr("width", width)
        .attr("height", height)
        .append("g")
        .attr("transform", `translate(${width / 2}, ${height / 2})`);

      const g = svg
        .selectAll(".arc")
        .data(pie(data))
        .enter()
        .append("g")
        .attr("class", (d) => d.data.sex.toLowerCase());

      g.append("path")
        .attr("d", arc)
        .style("fill", (d) => color(d.data.sex));

      g.append("text")
        .attr("transform", (d) => "translate(" + arc.centroid(d) + ")")
        .attr("text-anchor", "middle")
        .text((d) => d.data.sex);

      g.on("mousemove", this.showBar)
      g.on("mouseleave", this.clearBar)

    },
    clearPie() {
      d3.selectAll(".piechart svg").remove();
    },
    defaultPie() {
      this.sortingPopulationBySex(this.currentDataset);
      this.drawPie(this.populationBySex);
    },
    updatePie(index) {
      this.clearPie();
      this.currentDataset = this.data[index];
      this.sortingPopulationBySex(this.currentDataset);
      this.drawPie(this.populationBySex);
    },
    showBar(e, d){
      d3.select(e.currentTarget).attr("r", 8);
      this.showBarChart = true;
      this.barChartData = this.currentDataset[d.data.sex];
      this.updateBarChartPosition(e.clientX , e.clientY );
    },
    clearBar(){
      this.showBarChart = false;
    },
    updateBarChartPosition(x, y) {
      const chartContainer = this.$refs.chartContainer;
      const containerRect = chartContainer.getBoundingClientRect();
      const offsetX = x - containerRect.left;
      const offsetY = y - containerRect.top;
      this.barChartPosition = { x: offsetX, y: offsetY };
    }
  },
};
</script>

<style scoped>
.piechart-svg-container {
  position: relative;
  padding-bottom: 1%;
  vertical-align: top;
  width: 100%;
}
.chart_warpper{
  margin-bottom: 20px;
}
.btn_wrapper {
  margin: 5 10px;
  padding: 20px;
}

.btn_wrapper button {
  margin: 10px 40px;
  padding: 10px 20px;
  border-radius: 20px;
  color: black;
  background-color: white;
  border: 2px solid black;
  outline: none;
}
.btn_wrapper button.focus {
  color: white;
  background-color: black;
}
.bar{
  z-index: 99;
}
</style>
