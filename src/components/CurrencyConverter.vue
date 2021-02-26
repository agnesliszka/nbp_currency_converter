<template>
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
      <el-form class="date" label-position="top" label-width="130px">
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
      <el-button type="warning" @click="getChosenCurrencyExchangeRate()"
        >{{ chosenCurrencyExchangeRateTitle }}
      </el-button>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import axios from "axios";

@Component
export default class HelloWorld extends Vue {
  date: string = "";
  currencyList: Array<string> = [];
  currency: string = "";
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
    console.log(this.date);
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
    this.loading = true;
    await axios
      .get(
        // `http://api.nbp.pl/api/exchangerates/rates/a/gbp/${this.date}/?format=json`
        `http://api.nbp.pl/api/exchangerates/tables/a/2016-03-30/?format=json`
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
.currencyConverter {
  display: grid;
  grid-template-rows: 100px 30px;
}

.selectCurrencyAndDate {
  display: grid;
  grid-template-columns: repeat(3, calc(33%));
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
  display: grid;
  margin-right: 10px;
  grid-template-columns: repeat(5, calc(20%));
}
</style>
