<template>
  <div class="currencyConverter">
    <el-form label-position="top" label-width="130px">
      <el-form-item label="Currency converter">
        <el-date-picker v-model="date" type="date" format="dd-MMM-yyyy">
        </el-date-picker>
      </el-form-item>
    </el-form>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";

@Component
export default class HelloWorld extends Vue {
  date: string = "";

  getUSDexchangeRate(): void {
    axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/c/usd/${this.date}/?format=json`
    );
  }
  getEURexchangeRate(): void {
    axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/c/eur/${this.date}/?format=json`
    );
  }
  getCHFexchangeRate(): void {
    axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/c/chf/${this.date}/?format=json`
    );
  }
}
</script>
<style scoped></style>
