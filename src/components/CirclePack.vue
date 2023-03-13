<template>
  <svg :width="length" :height="length">
    <circle
      v-for="d in nodes"
      :transform="`translate(${d.x + 1},${d.y + 1})`"
      :r="d.r"
      :fill="d.height === 1 ? colorScale(d.data[0]) : 'white'"
      :opacity="d.height === 1 ? 1 : 0.5"
    ></circle>
    <text
      v-for="d in nodes"
      :transform="`translate(${d.x + 1},${d.y + 1})`"
      dy="3"
      text-anchor="middle"
    >
      {{ d.height === 1 ? d.data[0] : "" }}
    </text>
  </svg>
</template>

<script>
import * as d3 from "d3";
import data from "../assets/data.json";

export default {
  name: "CirclePack",
  data: () => ({
    length: 800,
  }),
  computed: {
    data() {
      return data;
    },
    legendData() {
      return [
        { category: "Critically Endangered", color: "#b30000" },
        { category: "Endangered", color: "#e34a33" },
        { category: "Vulnerable", color: "#fc8d59" },
        { category: "Near Threatened", color: "#fdbb84" },
        { category: "Least Concern", color: "#fdd49e" },
        { category: "Data Deficient", color: "#d9d9d9" },
      ];
    },
    categories() {
      return this.legendData.map((d) => d.category);
    },
    colors() {
      return this.legendData.map((d) => d.color);
    },
    colorScale() {
      return d3.scaleOrdinal().domain(this.categories).range(this.colors);
    },
    nodes() {
      const groupingFn = [(d) => d.redlistCategory, (d) => d.scientificName];
      const rollupData = d3.rollup(this.data, (v) => v.length, ...groupingFn);
      const childrenAccessorFn = ([key, value]) =>
        value.size && Array.from(value);
      const hierarchyData = d3
        .hierarchy(rollupData, childrenAccessorFn)
        .sum(([key, value]) => value)
        .sort((a, b) => b.value - a.value);
      const root = d3.pack().size([this.length, this.length]).padding(2)(
        hierarchyData
      );
      return root.descendants();
    },
  },
};
</script>

<style lang="css">
text {
  font-size: 14px;
  font-weight: 700;
  paint-order: stroke;
  stroke: white;
  stroke-width: 2px;
  stroke-linecap: butt;
  stroke-linejoin: miter;
}
</style>
