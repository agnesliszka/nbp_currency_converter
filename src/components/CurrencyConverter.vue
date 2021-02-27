<template>
  <div class="wrapper">
    <div class="currencyConverter">
      <div class="selectCurrencyAndDate">
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
        <el-form class="date">
          <el-form-item>
            <el-date-picker
              v-model="date"
              type="date"
              format="yyyy-MM-dd"
              :placeholder="today()"
            >
            </el-date-picker>
          </el-form-item>
        </el-form>
        <el-button
          class="acceptButton"
          type="success"
          @click="getCurrenciesList()"
          >getCurrenciesList</el-button
        >
      </div>
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
        <el-button type="warning" @click="getChosenCurrencyExchangeRate()"
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
  currencyList: Array<string> = [];
  currency: string = "";
  currencyToLowerCase: string = "";
  responseDataList: Array<string> = [];
  exchangeRate: string = "";
  loading: boolean = false;

  get chosenCurrencyExchangeRateTitle(): string {
    return `Get ${this.currency} currency rate`;
  }

  async mounted() {
    this.today();
    await this.getCurrenciesList();
  }

  @Watch("date")
  changeDateFormat(): void {
    this.date = new Date(this.date).toISOString().split("T")[0];
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
    console.log(this.date);
    // let exchangeRate: string = "";
    this.loading = true;
    await axios
      .get(
        "http://api.nbp.pl/api/exchangerates/rates/a/gbp/2012-01-02/"
        // `http://api.nbp.pl/api/exchangerates/rates/a/gbp/${this.date}/?format=json`
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
    axios
      .get(
        `http://api.nbp.pl/api/exchangerates/rates/a/${this.currencyToLowerCase}/${this.date}/?format=json`
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
}
</script>
<style scoped>
.wrapper {
  width: 70%;
  height: 70%;
  position: absolute;
  top: calc(50% - 35%);
  left: calc(50% - 35%);
  border-radius: 25px;
  border: 2px solid #73ad21;
  background-color: white;
}

.currencyConverter {
  display: grid;
  grid-template-rows: 100px 50px 50px 100px;
}

.selectCurrencyAndDate {
  margin-top: 60px;
  margin-left: 150px;
  display: grid;
  grid-template-columns: repeat(3, calc(25%));
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

.acceptButton {
  grid-row: 1 / span 2;
  grid-column: 3 / span 1;
  margin-right: 10px;
}

.buttons {
  margin: 60px;
  display: grid;
  margin-right: 50px;
  grid-template-columns: repeat(5, calc(25%));
}

.chosenCurrencyRate {
  margin-top: 120px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.displayExchangeRate {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-top: 170px;
}

.exchangeRate {
  font-size: 36px;
  text-align: center;
}
</style>
