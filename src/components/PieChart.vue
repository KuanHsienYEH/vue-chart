<template>
  <div class="piechart-svg-container" align="center">
    <div class="piechart"></div>
    <BarChart class="bar" :inputData='focusData'/>
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

export default {
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
    focusData:null
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
      let sum = 0;
      for (let item in data) {
        const distribution = Object.values(data[item]);
        const index = this.populationBySex.length;
        const total = distribution.reduce((acc, cur) => acc + cur);
        const obj = {
          sex: item,
          rowdata: data[item],
          population: total,
        };
        sum = sum + total
        this.populationBySex.splice(index, 0, obj);
      }
      this.populationBySex.forEach(el=>{
        el.percnetage = Math.round(el.population/sum*100)+'%';
      })
    },
    drawPie(data) {
      const width = 350,
        height = 350,
        radius = Math.min(width, height) / 2,
        color = d3.scaleOrdinal(d3.schemeCategory10);

      const arc = d3
        .arc()
        .innerRadius(radius - 120)
        .outerRadius(radius);

      const pie = d3
        .pie()
        .sort(null)
        .value((d) => d.population);

      const svg = d3
        .select(".piechart")
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
        .text((d) => d.data.sex +' : ' +d.data.percnetage)

      g.on("mouseover", (d)=>{
        this.focusData = d
      });
    },
    // focusedSex(e, d)){
    //   const arc =d3
    //     .arc()
    //     .innerRadius(radius - 90)
    //     .outerRadius(radius);

    //   d3.select(e.currentTarget).attr("d", arc);
    //   // this.currentFocusSex = `${d.text} is ${d.done ? 'done' : 'not done'}`;
    // },
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
  },
};
</script>

<style scoped>
.piechart-svg-container {
  display: inline-block;
  position: relative;
  width: 100%;
  padding-bottom: 1%;
  vertical-align: top;
  overflow: hidden;
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
</style>
