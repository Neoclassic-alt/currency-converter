<template>
  <div class="container">
    <v-progress-circular
      v-if="!currenciesInfo"
      indeterminate
      color="primary"
      class="progress-loader"
    />
    <div v-else class="content">
      <v-row class="mt-4">
        <p class="text-h4">
          {{ $t("currencyConventer") }}
        </p>
      </v-row>
      <v-row>
        <v-col cols="12" sm="3">
          <v-text-field
            v-model.number="giveAmount"
            :label="$t('give')"
            :placeholder="$t('inputAmount')"
            outlined
            @change="currencyConvert"
          />
        </v-col>
        <v-col cols="12" sm="2">
          <v-select
            v-model="currencies.giveCurrency"
            outlined
            :label="$t('currency')"
            :items="currencies.currenciesList"
            @change="currencyConvert"
          />
        </v-col>
        <v-col cols="12" sm="1" class="ml-2 mt-2 mr-4" style="max-width: 4%">
          <v-btn icon @click="swap">
            <v-icon>mdi-swap-horizontal</v-icon>
          </v-btn>
        </v-col>
        <v-col cols="12" sm="3">
          <v-text-field
            v-model.number="getAmount"
            :label="$t('get')"
            outlined
            disabled
          />
        </v-col>
        <v-col cols="12" sm="2">
          <v-select
            v-model="currencies.getCurrency"
            outlined
            :label="$t('currency')"
            :items="currencies.currenciesList"
            @change="currencyConvert"
          />
        </v-col>
      </v-row>
      <v-row class="mt-4">
        <p class="text-h4">
          {{ $t('exchangeRates') }}
        </p>
      </v-row>
      <v-row>
        <v-col sm="2" class="mt-3">
          {{ $t('baseCurrency') }}:
        </v-col>
        <v-col sm="2">
          <v-select
            v-model="currencies.baseCurrency"
            outlined
            :label="$t('currency')"
            :items="currencies.currenciesList"
            @change="currentCurrencies"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>{{ $t('state') }} {{ currentDate }}: {{ currencies.rates }} </v-col>
      </v-row>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      currencies: {
        currenciesList: ['RUB', 'EUR', 'USD', 'GBP', 'JPY', 'CNY'],
        giveCurrency: 'RUB',
        getCurrency: 'USD',
        baseCurrency: 'USD',
        rates: ''
      },
      currenciesInfo: null,
      giveAmount: 1000,
      getAmount: 1
    }
  },
  computed: {
    currentDate () {
      const date = new Date()
      const formatter = new Intl.DateTimeFormat('ru', {
        year: 'numeric',
        month: 'numeric',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit'
      })
      return formatter.format(date)
    }
  },
  mounted () {
    const myHeaders = new Headers()
    myHeaders.append('apikey', 'spp8OKMkJR1GEyoKI7hqvA9Cd4qwLmlc')

    const requestOptions = {
      method: 'GET',
      redirect: 'follow',
      headers: myHeaders
    }

    fetch('https://api.apilayer.com/fixer/latest?base=USD&symbols=RUB,EUR,GBP,JPY,CNY', requestOptions)
      .then(res => res.json())
      .then((result) => {
        this.currenciesInfo = result
        this.currentCurrencies()
        this.currencyConvert()
      })
  },
  methods: {
    currentCurrencies () {
      let str = ''
      this.currenciesInfo.rates.USD = 1
      for (const i of this.currencies.currenciesList) {
        if (i !== this.currencies.baseCurrency) {
          str += (this.currenciesInfo.rates[i] / this.currenciesInfo.rates[this.currencies.baseCurrency]).toFixed(4) + ' ' + i + ' · '
        }
      }
      str = str.slice(0, -2)
      this.currencies.rates = str
    },
    currencyConvert () {
      this.currenciesInfo.rates.USD = 1
      this.getAmount = (this.giveAmount * (this.currenciesInfo.rates[this.currencies.getCurrency] / this.currenciesInfo.rates[this.currencies.giveCurrency])).toFixed(4)
    },
    swap () {
      [this.getAmount, this.giveAmount] = [this.giveAmount, this.getAmount];
      [this.currencies.giveCurrency, this.currencies.getCurrency] = [this.currencies.getCurrency, this.currencies.giveCurrency]
    }
  }
}
</script>

<style scoped>
  .container {
    margin: 0 auto;
    max-width: 1200px;
  }
  .progress-loader {
    margin-left: 50%;
  }
</style>
