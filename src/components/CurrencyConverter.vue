<template>
  <div class="wrapper">
    <div class="title">NBP currency rates converter</div>
    <div class="currencyConverter">
      <el-form class="selectCurrencyAndDate">
        <el-select
          class="currency"
          v-model="currency"
          placeholder="Select currency"
        >
          <el-option
            v-for="currency in currencyList"
            :key="currency"
            :label="currency"
            :value="currency"
          >
          </el-option>
        </el-select>
        <el-form-item class="date">
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
      <div class="chosenCurrencyRate">
        <el-button type="success" @click="getChosenCurrencyExchangeRate()"
          >{{ chosenCurrencyExchangeRateTitle }}
        </el-button>
      </div>
      <div class="displayExchangeRate">
        <div class="exchangeRateText">Exchane rate for {{ date }}</div>
        <div class="exchangeRate">{{ exchangeRate }}</div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import axios from "axios";

@Component
export default class HelloWorld extends Vue {
  date: string = new Date().toISOString().split("T")[0];
  dayOfTheWeek: number | undefined;
  currencyList: Array<string> = [];
  currency: string = "";
  currencyToLowerCase: string = "";
  responseDataList: Array<string> = [];
  exchangeRate: string = "";
  loading: boolean = false;
  validationFailed: boolean = false;

  get chosenCurrencyExchangeRateTitle(): string {
    return `Get ${this.currency} currency rate`;
  }

  async mounted() {
    this.today();
    this.dayOfTheWeek = new Date(this.date).getDay();
    if (this.dayOfTheWeek === 5 || this.dayOfTheWeek === 6) {
      this.$notify({
        title: "Warning",
        message:
          "Exchange rate cannot be taken for Saturday and Sunday. Please choose another day.",
        type: "warning",
      });
    }
    await this.getCurrenciesList();
  }

  @Watch("date")
  changeDateFormat(): void {
    const day = ("0" + new Date(this.date).getDate()).slice(-2);
    const month = ("0" + (new Date(this.date).getMonth() + 1)).slice(-2);
    this.date = new Date(this.date).getFullYear() + "-" + month + "-" + day;

    this.dayOfTheWeek = new Date(this.date).getDay();

    if (this.dayOfTheWeek === 6 || this.dayOfTheWeek === 0) {
      this.$notify({
        title: "Warning",
        message:
          "Exchange rate cannot be taken for Saturday and Sunday. Please choose another day.",
        type: "warning",
      });
    }
  }

  @Watch("currency")
  changeCurrencyToLowerCase(): void {
    this.currencyToLowerCase = this.currency.toLowerCase();
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
      .then((response: any) => (this.exchangeRate = response.data.rates[0].mid))
      .catch((exception) => {
        this.$notify.error({
          title: "Error",
          message: exception,
        });
      })
      .finally(() => {
        this.loading = false;
      });
  }

  getEURexchangeRate(): void {
    axios
      .get(
        `http://api.nbp.pl/api/exchangerates/rates/a/eur/${this.date}/?format=json`
      )
      .then((response: any) => (this.exchangeRate = response.data.rates[0].mid))
      .catch((exception) => {
        this.$notify.error({
          title: "Error",
          message: exception,
        });
      })
      .finally(() => {
        this.loading = false;
      });
  }

  getCHFexchangeRate(): void {
    axios
      .get(
        `http://api.nbp.pl/api/exchangerates/rates/a/chf/${this.date}/?format=json`
      )
      .then((response: any) => (this.exchangeRate = response.data.rates[0].mid))
      .catch((exception) => {
        this.$notify.error({
          title: "Error",
          message: exception,
        });
      })
      .finally(() => {
        this.loading = false;
      });
  }

  getUSDexchangeRate(): void {
    axios
      .get(
        `http://api.nbp.pl/api/exchangerates/rates/a/usd/${this.date}/?format=json`
      )
      .then((response: any) => (this.exchangeRate = response.data.rates[0].mid))
      .catch((exception) => {
        this.$notify.error({
          title: "Error",
          message: exception,
        });
      })
      .finally(() => {
        this.loading = false;
      });
  }
  getChosenCurrencyExchangeRate(): void {
    if (this.currency === "") {
      this.$notify.error({
        title: "Error",
        message: "Please select currency",
      });
    } else if (this.dayOfTheWeek === 6 || this.dayOfTheWeek === 6) {
      this.$notify.error({
        title: "Error",
        message: "Please input day different than Saturday or Sunday",
      });
    } else {
      axios
        .get(
          `http://api.nbp.pl/api/exchangerates/rates/a/${this.currencyToLowerCase}/${this.date}/?format=json`
        )
        .then(
          (response: any) => (this.exchangeRate = response.data.rates[0].mid)
        )
        .catch((exception) => {
          this.$notify.error({
            title: "Error",
            message: exception,
          });
        })
        .finally(() => {
          this.loading = false;
        });
    }
  }
}
</script>
<style scoped>
.exchangeRate {
  font-size: 36px;
  text-align: center;
}

/* Mobile Styles */
@media (max-width: 400px) {
  .wrapper {
    width: 80%;
    height: 90%;
    position: absolute;
    top: calc(50% - 45%);
    left: calc(50% - 40%);
    border-radius: 25px;
    border: 2px solid #73ad21;
    background-color: rgb(253, 253, 253);
  }
  .title {
    margin-top: 10px;
    margin-bottom: 10px;
    height: 16px;
    font-size: 16px;
  }

  .currencyConverter {
    margin-top: 0px;
    margin-left: 35px;
    display: grid;
    grid-template-rows: 100px 220px 40px 40px;
  }

  .selectCurrencyAndDate {
    margin-top: 10px;
    display: grid;
    margin-left: 1px;
    grid-template-columns: 220px;
    grid-template-rows: 40px 40px;
    row-gap: 15px;
  }

  .currency {
    grid-row: 1 / span 1;
    grid-column: 1 / span 1;
  }

  .date {
    grid-row: 2 / span 1;
    grid-column: 1 / span 1;
  }

  .buttons {
    margin-left: 0px;
    margin-top: 20px;
    margin-right: 30px;
    display: grid;
    grid-template-rows: 40px;
    grid-template-columns: 220px;
    row-gap: 10px;
  }

  .el-button + .el-button {
    margin-left: 0px;
  }

  .chosenCurrencyRate {
    margin-right: 30px;
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .displayExchangeRate {
    font-size: 14px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-top: 50px;
    margin-right: 50px;
  }
}

/* Tablet Styles */
@media (min-width: 401px) and (max-width: 960px) {
  .wrapper {
    width: 60%;
    height: 60%;
    position: absolute;
    top: calc(50% - 30%);
    left: calc(50% - 30%);
    border-radius: 25px;
    border: 2px solid #73ad21;
    background-color: rgb(253, 253, 253);
  }
  .title {
    height: 36px;
    font-size: 36px;
    margin-top: 20px;
    margin-bottom: 40px;
  }

  .currencyConverter {
    margin-top: 0px;
    margin-left: 35px;
    display: grid;
    grid-template-rows: 100px 120px 80px 40px;
  }

  .selectCurrencyAndDate {
    margin-top: 40px;
    margin-left: calc(50% - 200px);
    display: grid;
    grid-template-columns: repeat(2, calc(25%));
    grid-template-rows: 30px 30px;
    column-gap: 10px;
    row-gap: 10px;
  }

  .currency {
    grid-row: 1 / span 1;
    grid-column: 1 / span 1;
  }

  .date {
    grid-row: 1 / span 1;
    grid-column: 2 / span 1;
  }

  .buttons {
    margin-left: 0px;
    margin-top: 20px;
    margin-right: 30px;
    display: grid;
    grid-template-rows: 40px;
    grid-template-columns: repeat(2, 50%);
    column-gap: 10px;
    row-gap: 20px;
  }

  .el-button + .el-button {
    margin-left: 0px;
  }

  .chosenCurrencyRate {
    margin-right: 30px;
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .displayExchangeRate {
    font-size: 14px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-top: 50px;
    margin-right: 50px;
  }
}
/* Desktop Styles */
@media (min-width: 961px) {
  .wrapper {
    margin: 0 auto;
    width: 70%;
    height: 70%;
    position: absolute;
    top: calc(50% - 35%);
    left: calc(50% - 35%);
    border-radius: 25px;
    border: 2px solid #73ad21;
    background-color: rgb(253, 253, 253);
  }

  .title {
    margin-top: 20px;
    font-size: 36px;
  }

  .currencyConverter {
    display: grid;
    grid-template-rows: 100px 50px 50px 100px;
  }

  .selectCurrencyAndDate {
    margin-top: 40px;
    margin-left: calc(50% - 200px);
    display: grid;
    grid-template-columns: repeat(2, calc(25%));
    grid-template-rows: 30px 30px;
    column-gap: 10px;
    row-gap: 10px;
  }

  .currency {
    grid-row: 1 / span 1;
    grid-column: 1 / span 1;
  }

  .date {
    grid-row: 1 / span 1;
    grid-column: 2 / span 1;
  }

  .buttons {
    margin-left: 50px;
    display: grid;
    margin-right: 50px;
    grid-template-columns: repeat(4, 25%);
  }

  .chosenCurrencyRate {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .displayExchangeRate {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-top: 100px;
  }
}
</style>
