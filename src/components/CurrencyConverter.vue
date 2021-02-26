<template>
  <div class="currencyConverter">
    <span>Currency</span>
    <el-select v-model="currency" placeholder="Select currency">
      <el-option
        v-for="currency in currencyList"
        :key="currency"
        :label="currency"
        :value="currency"
      >
      </el-option>
    </el-select>
    <el-form label-position="top" label-width="130px">
      <el-form-item label="Currency converter">
        <el-date-picker
          v-model="date"
          type="date"
          format="yyyy-MM-dd"
          :placeholder="today()"
        >
        </el-date-picker>
      </el-form-item>
    </el-form>
    <div class="buttons">
      <el-button type="success" round @click="getCurrenciesList()"
        >getCurrenciesList</el-button
      >
      <el-button type="primary" @click="getGBPexchangeRate()"
        >Get GBP currency rate</el-button
      >
      <el-button type="primary" @click="getEURexchangeRate()"
        >Get EUR currency rate</el-button
      >
      <el-button type="primary" @click="getCHFexchangeRate()"
        >Get CHF currency rate</el-button
      >
      <el-button type="primary" @click="getUSDexchangeRate()"
        >Get USD currency rate</el-button
      >
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";

@Component
export default class HelloWorld extends Vue {
  date: string = "";
  currencyList: Array<string> = [];
  currency: string = "";
  loading: boolean = false;

  async mounted() {
    this.today();
    await this.getCurrenciesList();
  }

  today() {
    const today = new Date().toISOString().split("T")[0];
    return today;
  }

  async getCurrenciesList(): Promise<void> {
    this.loading = true;
    const currencyList: Array<string> = [];
    const url = await fetch("https://api.nbp.pl/api/exchangerates/tables/a/");
    await url
      .json()
      .then((response) => {
        response[0].rates.forEach(
          (el: { currency: string; code: string; mid: string }) => {
            currencyList.push(el.code);
          }
        );
        this.currencyList = [...currencyList];
      })
      .catch((exception: string) => {
        this.$notify.error({
          title: "Error",
          message: exception,
        });
      })
      .finally(() => {
        this.loading = false;
      });
  }

  async getGBPexchangeRate(): Promise<void> {
    this.loading = true;
    await axios
      .get(
        `http://api.nbp.pl/api/exchangerates/rates/a/gbp/${this.date}/?format=json`
      )
      .then((response) => {
        console.log("@response");
        console.log(response);
        // if (response.errorCode === 0) {
        //   response.result.forEach((el) => this.itemTypes.push(el.value));
        // } else {
        //   const msg = `${response.errorCode}: ${response.errorMessage}`;
        //   this.$notify.error({
        //     title: "Error",
        //     message: "This is an error message",
        //   });
        // }
      })
      .catch((exception) => {
        this.$notify.error({
          title: "Error",
          message: exception,
        });
      })
      .finally(() => {
        this.loading = false;
      });

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
  getChosenCurrencyExchangeRate(): void {
    const chosenCurrencyExchangeRate = axios.get(
      `http://api.nbp.pl/api/exchangerates/rates/c/${this.currency}/${this.date}/?format=json`
    );
    console.log(chosenCurrencyExchangeRate);
  }
}
</script>
<style scoped>
.buttons {
  width: 100%;
  display: flex;
  flex-grow: 1;
  justify-items: space-between;
}
</style>
