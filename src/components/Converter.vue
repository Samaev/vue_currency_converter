<template>
  <div class="converter">
    <h2>Currency Exchange</h2>
    <div>
      <div>
        <input type="number" v-model="currency1" v-on:input="setCurrency2" placeholder="Enter to exchange">
        <select v-model="rate1" v-on:change="setCurrency1">
          <option v-for="(currency,index) in initialCurrencies" v-bind:key="index" >{{currency}}</option>
        </select>
      </div>
      <div>
        <input type="number" v-model="currency2" placeholder="Enter to exchange" v-on:input="setCurrency1">
        <select v-model="rate2" v-on:change="setCurrency2">
          <option v-for="(currency,index) in initialCurrencies" v-bind:key="index">{{currency}}</option>
        </select>
      </div>
      <div>
        {{ currency1 }} {{ rate1 }} have been converted to {{ currency2 }} {{ rate2 }}
      </div>
    </div>
    <p v-if="limitAmountError">Ups</p>
  </div>
</template>

<script>
export default {
  name: 'ConvertArea',
  props: {
  },
  data: function(){
    return {
      initialCurrencies: ['USD',
        'EUR',
        'UAH',
        'GBP',
        'BTC',
        'ETH',
        'BNB',
        'XRP'
      ],
      currency1: 10000,
      currency2: 1,
      rates: {},
      rate1: 'USD',
      rate2: 'BTC',
      limitAmountError: false,
    }


    },
  methods: {
    getRates: function() {
      let that = this;
      const requestURL = 'https://api.currencyfreaks.com/latest?apikey=864b5a1dc98f4d0d94e5ea87ad2c1e0e';
      const request = new XMLHttpRequest();
      request.open('GET', requestURL);
      request.responseType = 'json';
      request.send();

      request.onload = function() {
        const response = request.response;
        that.rates = response.rates;
        that.setCurrency2();
      };
    },
    setCurrency1: function() {
      if(this.currency2 / 10000 > this.rates[this.rate2]) {
        this.limitAmountError = true;
        this.currency2 = Math.round(10000 * this.rates[this.rate2]*100)/100;
      }
      if (this.rate1 != 'BTC') {
        this.currency1 = Math.round(this.currency2 * this.rates[this.rate1] * (1/this.rates[this.rate2])*100)/100;
      } else {
        this.currency1 = Math.round(this.currency2 * this.rates[this.rate1] * (1/this.rates[this.rate2])*1000000)/1000000;
      }

    },
    setCurrency2: function() {
      if (this.rate2 != 'BTC') {
        this.currency2 = Math.round(this.currency1 * (1/this.rates[this.rate1]) * this.rates[this.rate2]*100)/100;
      } else {
        this.currency2 = Math.round(this.currency1 * this.rates[this.rate2] * (1/this.rates[this.rate1])*1000000)/1000000;
    }
    }
  },
  mounted() {
    this.getRates();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
