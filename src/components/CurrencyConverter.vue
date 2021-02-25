<template>
  <div class="currencyConverter">
    <el-form label-position="top" label-width="130px">
      <el-form-item label="Currency converter">
        <el-date-picker v-model="date" type="date" format="yyyy-MM-dd">
        </el-date-picker>
      </el-form-item>
    </el-form>
    <button @click="getGBPexchangeRate()">Get GBP currency rate</button>
    <button @click="getEURexchangeRate()">Get EUR currency rate</button>
    <button @click="getCHFexchangeRate()">Get CHF currency rate</button>
    <button @click="getUSDexchangeRate()">Get USD currency rate</button>
    <button @click="test()">Test</button>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";

@Component
export default class HelloWorld extends Vue {
  date: string = "";

  getGBPexchangeRate(): void {
    console.log(this.date);
    const gbpExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/gbp/${this.date}/?format=json`
    );
    console.log(gbpExchangeRates);
  }
  getEURexchangeRate(): void {
    const eurExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/eur/${this.date}/?format=json`
    );
    console.log(eurExchangeRates);
  }
  getCHFexchangeRate(): void {
    const chfExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/chf/${this.date}/?format=json`
    );
    console.log(chfExchangeRates);
  }
  getUSDexchangeRate(): void {
    const usdExchangeRates = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/a/usd/${this.date}/?format=json`
    );
    console.log(usdExchangeRates);
  }
  test(): void {
    const test = axios.get(
      "http://api.nbp.pl/api/exchangerates/rates/c/usd/2016-04-04/?format=json"
    );
    console.log(test);
  }
}
</script>
<style scoped></style>
