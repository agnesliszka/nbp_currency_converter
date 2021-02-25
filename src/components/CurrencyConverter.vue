<template>
  <div class="currencyConverter">
    <el-form label-position="top" label-width="130px">
      <el-form-item label="Currency converter">
        <el-date-picker v-model="date" type="date" format="dd-MMM-yyyy">
        </el-date-picker>
      </el-form-item>
    </el-form>
    <button>Get GBP currency rate</button>
    <button>Get EUR currency rate</button>
    <button>Get CHF currency rate</button>
    <button>Get USD currency rate</button>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";

@Component
export default class HelloWorld extends Vue {
  date: string = "";

  getGBPexchangeRate(): void {
    const gbpExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/gbp/${this.date}/?format=json`
    );
    console.log(gbpExchangeRates);
  }
  getEURexchangeRate(): void {
    const eurExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/eur/${this.date}/?format=json`
    );
  }
  getCHFexchangeRate(): void {
    const chfExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/chf/${this.date}/?format=json`
    );
  }
  getUSDexchangeRate(): void {
    const usdExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/usd/${this.date}/?format=json`
    );
  }
}
</script>
<style scoped></style>
