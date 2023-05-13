<template>
  <div class="barchart-svg-container" align="center">
    <div ref="barChartContainer"></div>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  props: {
    data: Object,
  },
  data(){
    return{
      currentData:[]
    }
  },
  mounted() {
    //console.log(this.data);
    this.fomatData()
    this.drawBar(this.currentData);
  },
  // beforeUnmount() {
  //   this.clearBar()
  // },
  methods: {
    fomatData(){
      this.currentData = Object.entries(this.data).map(([age, value]) => ({ age, value }));
    },
    drawBar(data) {
      const width = 500,
        height = 200,
        margin = 20,
        innerWidth = width - margin * 2,
        innerHeight = height - margin * 2;

      const chartContainer = this.$refs.barChartContainer; 

      const svg = d3
        .select(chartContainer)
        .append("svg")
        .attr("width", width)
        .attr("height", height);

      const max = d3.max(data, (d) => d.value);

      const xScale = d3.scaleLinear().domain([0, max]).range([0, innerWidth]);

      const xAxis = d3.axisBottom(xScale);

      const yScale = d3
        .scaleBand()
        .domain(data.map((d) => d.age))
        .range([0, innerHeight])
        .paddingInner(0.2)
        .paddingOuter(0.3)
        .round(true);

      const yAxis = d3.axisLeft(yScale);

      const g = svg
        .append("g")
        .attr("transform", `translate(${margin}, ${margin})`);

      g.append("g")
        .attr("transform", `translate(${margin},  ${innerHeight})`)
        .attr("class", "xaxis")
        .call(xAxis);

      g.append("g")
        .attr("transform", `translate(${margin},  0)`)
        .attr("class", "yaxis")
        .call(yAxis);

      g.selectAll("rect")
        .data(data)
        .enter()
        .append("rect")
        .attr("class", "bar")
        .attr("fill", "#d62828")
        .attr("x", `${margin + 1}`)
        .attr("y", (d) => yScale(d.age))
        .attr("width", (d) => xScale(d.value))
        .attr("height", yScale.bandwidth());

    },
    clearBar() {
      d3.selectAll(".barchart svg").remove();
    },
  },
};
</script>

<style scoped>
.barchart-svg-container {
  background-color: gray;
  opacity: 0.7;
  position: absolute;
  padding: 20px;
}
</style>
