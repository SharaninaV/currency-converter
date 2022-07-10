<template>
  <div>
      <h4 :class="$tt('headline4')">
        1 {{ this.currencyFrom }} = {{ this.rate }} {{ this.currencyTo }}
      </h4>
      <ui-grid>
        <ui-grid-cell columns="6">
          <ui-textfield
            inputType="number"
            v-model="amountFrom"
            @input="handleInputChange()"
          />
        </ui-grid-cell>
        <ui-grid-cell columns="6">
          <ui-select
            name="currencyFrom"
            id="currencyFrom"
            v-model="currencyFrom"
            :options="currencies"
            @selected="convert()"
          />
        </ui-grid-cell>
      </ui-grid>
      <ui-grid>
        <ui-grid-cell columns="5"/>
        <ui-grid-cell align="middle" columns="2">
          <ui-icon-button
            class="button"
            icon="swap_vert"
            @click="switchCurrencies()"
          />
        </ui-grid-cell>
        <ui-grid-cell columns="5"/>
      </ui-grid>
      <ui-grid>
        <ui-grid-cell columns="6">
          <ui-textfield disabled v-model="amountTo"/>
        </ui-grid-cell>
        <ui-grid-cell columns="6">
          <ui-select
            name="currencyTo"
            id="currencyTo"
            v-model="currencyTo"
            :options="currencies"
            @selected="convert()"
          />
        </ui-grid-cell>
      </ui-grid>
    </div>
</template>

<script>
import { CURRENCIES } from '@/constants'
import { countriesCurrencies } from '@/countriesCurrencies'

export default {
  name: 'CurrencyConverter',
  data () {
    return {
      exchangeRateUSDData: {},
      currencyFrom: null,
      currencyTo: 'EUR',
      rate: null,
      amountFrom: 0,
      amountTo: 0,
      currencies: CURRENCIES,
      countriesCurrencies,
      locale: null
    }
  },
  methods: {
    fetchData () {
      fetch(`https://openexchangerates.org/api/latest.json?app_id=${process.env.VUE_APP_OPEN_EXCHANGE_RATES_APP_ID}`)
        .then(response => response.json())
        .then(data => {
          this.exchangeRateUSDData = data.rates
          this.rate = (1 / this.exchangeRateUSDData[this.currencyFrom] * this.exchangeRateUSDData[this.currencyTo]).toFixed(4)
        })
    },
    handleInputChange () {
      this.getAbs()
      this.convert()
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
    getAbs () {
      this.amountFrom = !!this.amountFrom && Math.abs(this.amountFrom) >= 0 ? Math.abs(this.amountFrom) : null
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
  }
}
</script>
