<template>
  <div id="app">
    <CurrencyConverter />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import CurrencyConverter from "./components/CurrencyConverter.vue";

@Component({
  components: {
    CurrencyConverter,
  },
})
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0px;
  padding: 0px;
  min-width: 100vw;
  min-height: 100vh;
  background-image: url("./assets/background.jpg");
}
</style>
