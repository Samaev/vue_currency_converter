<template>
  <div class="card m-auto mb-5" style="width: 18rem;">

    <div class="card-header">
      <h2>Currency Exchange</h2>
    </div>
    <div class="card-body">
      <div>
        <input class="form-control mb-2" type="number" v-model="currency1" v-on:input="setCurrency2" placeholder="Enter to exchange">
        <select class="form-select mb-2" v-model="rate1" v-on:change="setCurrency1">
          <option v-for="(currency,index) in initialCurrencies" v-bind:key="index" >{{currency}}</option>
        </select>
      </div>
      <div>
        <input class="form-control mb-2" type="number" v-model="currency2" placeholder="Enter to exchange" v-on:input="setCurrency1">
        <select class="form-select mb-2" v-model="rate2" v-on:change="setCurrency2">
          <option v-for="(currency,index) in initialCurrencies" v-bind:key="index">{{currency}}</option>
        </select>
      </div>
      <div>

        <div class="alert alert-success" role="alert">
          <h4 class="alert-heading">Well done!</h4>
          <p>{{ currency1 }} {{ rate1 }} have been converted to {{ currency2 }} {{ rate2 }}</p>
          <hr>
          <p class="mb-0">Whenever you need to, be sure to use our service - you're welcome.</p>
        </div>
      </div>
    </div>
    <div v-if="limitAmountError">
      <div class="alert alert-danger d-flex align-items-center m-4 p-2" role="alert">
          Our rules allow exchanging the equivalent of a maximum of $10,000 per session. Please try to revise the amount downward and enter again
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'ConvertArea',
  props: {
    rates: Object,
  },
  data: function(){
    return {
      initialCurrencies: ['USD',
        'EUR',
        'UAH',
        'GBP',
        'BTC',
        'ETH',
        'XRP'
      ],
      currency1: 10000,
      currency2: 1,
      rate1: 'USD',
      rate2: 'BTC',
      limitAmountError: false,
    }


    },
  methods: {

    setCurrency1: function() {
      this.limitAmountError = false;
      if(this.currency2 / 10000 > this.rates[this.rate2]) {
        this.limitAmountError = true;
        this.currency2 = Math.round(10000 * this.rates[this.rate2]*100)/100;
      }
      if (this.rates[this.rate1] < 1) {
        this.currency1 = Math.round(this.currency2 * this.rates[this.rate1] * (1/this.rates[this.rate2])*100)/100;
      } else {
        this.currency1 = Math.round(this.currency2 * this.rates[this.rate1] * (1/this.rates[this.rate2])*1000000)/1000000;
      }

    },
    setCurrency2: function() {
      this.limitAmountError = false;
      if(this.currency1 / 10000 > this.rates[this.rate1]) {
        this.limitAmountError = true;
        this.currency1 = Math.round(10000 * this.rates[this.rate1]*100)/100;
      }
      if (this.rates[this.rate2] < 1) {
        this.currency2 = Math.round(this.currency1 * (1/this.rates[this.rate1]) * this.rates[this.rate2]*100)/100;
      } else {
        this.currency2 = Math.round(this.currency1 * this.rates[this.rate2] * (1/this.rates[this.rate1])*1000000)/1000000;
      }
    }
  },
  mounted() {
    setTimeout(()=>this.setCurrency2(),1500);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
