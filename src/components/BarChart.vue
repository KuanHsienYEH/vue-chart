<template>
  <div class="barchart-svg-container" align="center">
    <div class="barchart"></div>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  props: {
    inputData: Object,
  },
  data: () => ({
    distribution:[],
    mockDistribution:[
        {
          age: "18-24",
          value: 42345,
          percentage: "25.21%",
        },
        {
          age: "25-34",
          value: 63234,
          percentage: "25.21%",
        },
      ],
  }),
  watch :{
    inputData(newValue,oldValue){
      const hasOldValue = !!oldValue;
      if(hasOldValue){
        this.clearBar();
        this.distribution = [];
      }
      for(let item in this.inputData.data.rowdata){
        let obj = {
          age : item,
          value: this.inputData.data.rowdata[item]
        }
        this.distribution.push(obj)
      }
      this.drawBar(this.distribution)
    }
  },
  methods: {
    drawBar(data) {
      const width = 500,
        height = 250,
        margin = 20,
        innerWidth = width - margin * 2,
        innerHeight = height - margin * 2;

      const svg = d3
        .select(".barchart")
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
        .attr("fill", "orange")
        .attr("x", `${margin + 1}`)
        .attr("y", (d) => yScale(d.age))
        .attr("width", (d) => xScale(d.value))
        .attr("height", yScale.bandwidth());

      // //???
      // g.selectAll("text")
      //   .data(data)
      //   .enter()
      //   .append("text")
      //   .text((d) => d.percentage)
      //   .attr("fill", "#000")
      //   .attr("x", `${margin + 10}`)
      //   .attr("y", (d) => yScale(d.age));
    },
    clearBar() {
      d3.selectAll(".barchart svg").remove();
    },
  },
};
</script>

<style scoped>
.barchart-svg-container {
  display: inline-block;
  position: absolute;
  top:0;
  left:0;
  padding-bottom: 1%;
  vertical-align: top;
  overflow: hidden;
}
</style>
