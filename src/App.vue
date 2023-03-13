<template>
  <div id="chart">
    <CirclePack />
  </div>
</template>

<script>
import CirclePack from "./components/CirclePack.vue";

export default {
  name: "app",
  components: {
    CirclePack,
  },
};
</script>

<style lang="css">
#chart {
  text-align: center;
  font-family: "Source Sans Pro", sans-serif;
}
</style>
