<template>
  <div id="app">
    <h1>Covid-19 Data Visualizer</h1>
    <active-cases />
  </div>
</template>

<script>
import ActiveCases from "./components/ActiveCases.vue";

export default {
  name: "App",
  components: {
    ActiveCases
  }
};
</script>

<style>
@import "../node_modules/bulma/css/bulma.css";
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
