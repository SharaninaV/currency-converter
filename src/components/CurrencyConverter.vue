<template>
  <div>
      <h4 :class="$tt('headline4')">1 {{ this.currencyFrom }} = {{ this.rate }} {{ this.currencyTo }}</h4>
    <ui-grid position="center">
      <ui-grid-cell columns="2">
        <ui-textfield type="number" v-model="amountFrom" @change="convert()" @input="convert()"></ui-textfield>
      </ui-grid-cell>
      <ui-grid-cell columns="2">
        <ui-select
          name="currencyFrom"
          id="currencyFrom"
          v-model="currencyFrom"
          :options="currencies"
          @change="convert()"
        >
      </ui-select>
      </ui-grid-cell>
      <ui-grid-cell columns="2" align="middle">
        <ui-button icon="swap_horiz" @click="switchCurrencies()">Swap</ui-button>
      </ui-grid-cell>
      <ui-grid-cell columns="2">
        <ui-textfield type="number" disabled v-model="amountTo"></ui-textfield>
      </ui-grid-cell>
      <ui-grid-cell columns="2">
        <ui-select
          name="currencyTo"
          id="currencyTo"
          v-model="currencyTo"
          :options="currencies"
          @change="convert()"
        >
        </ui-select>
      </ui-grid-cell>
    </ui-grid>
    </div>
</template>

<script>
import { rates } from '@/rates-usd'
import { currencies } from '@/constants'
import { countriesCurrencies } from '@/countriesCurrencies'

export default {
  name: 'CurrencyConverter',
  data () {
    return {
      exchangeRateUSDData: {},
      currencyFrom: 'EUR',
      currencyTo: 'EUR',
      rate: null,
      amountFrom: 1,
      amountTo: null,
      rates,
      currencies,
      countriesCurrencies,
      locale: null
    }
  },
  methods: {
    fetchData () {
      // eslint-disable-next-line no-template-curly-in-string
      // fetch('https://openexchangerates.org/api/latest.json?app_id=f8fb7690311c4f7c95475f6da420a307')
      //   .then(response => response.json())
      //   .then(data => console.log(data))
      // console.log(rates)
      console.log(rates)
      this.exchangeRateUSDData = rates.rates
      // this.rate = rates.rates[this.currencyTo]
      // return rates
    },
    convert () {
      this.rate = (this.exchangeRateUSDData[this.currencyTo]).toFixed(4)
      if (this.currencyFrom === 'USD') {
        this.amountTo = (this.amountFrom * this.rate).toFixed(2)
      } else {
        this.amountTo = (this.amountFrom / this.exchangeRateUSDData[this.currencyFrom] * this.rate).toFixed(2)
        this.rate = (this.rate / this.exchangeRateUSDData[this.currencyFrom]).toFixed(4)
      }
    },
    switchCurrencies () {
      [this.currencyFrom, this.currencyTo] = [this.currencyTo, this.currencyFrom]
      this.convert()
    },
    setCurrencyByLocale () {
      const locale = (navigator.language || navigator.userLanguage).slice(-2).toUpperCase()
      this.countriesCurrencies[locale]
        ? this.currencyFrom = this.countriesCurrencies[locale][0].code
        : this.currencyFrom = 'USD'
      if (this.currencyFrom === 'EUR') this.currencyTo = 'USD'
    }
  },
  mounted () {
    this.fetchData()
    this.setCurrencyByLocale()
    this.convert()
  }
}
</script>

<style>
h4 {
  margin-left: 20px;
}
</style>
