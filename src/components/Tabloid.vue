<template>
  Tabloid will be there
  <div>
    <button v-for="(ticket, index) in tickets" v-bind:key="index" v-on:click="setTabloid(ticket)">{{ ticket }}</button>
  </div>
  <div>
    <h4>Currency tabloid</h4>
    <div v-for="(currency,index) in currencies" v-bind:key="index">{{ currency }} - {{ setCurrency(currency) }}</div>
    <button v-on:click="showPopup = true">Add currency</button>
  </div>
  <button v-on:click="refreshRates" v-if="counter === 5">Refresh</button>
  <p v-if="counter !== 5"> Currencies can be updated in {{ counter }} seconds. Please wait</p>
  <div v-if="showPopup" class="popup">
    <div class="popup-button">
      <input type="text" v-model="searchTerm" placeholder="Enter the ticker">
      <select v-if="searchTerm.length > 0" multiple v-on:click="currencies.push(selectedTicket[0])" v-model="selectedTicket">
        <option
            v-for="(ticket,index) in arrayRates.filter(tick=>tick[0].toLowerCase().includes(searchTerm))"
            v-bind:key="index"
            v-bind:value="ticket[0]"
        >
          {{ ticket[0] }}
        </option>
      </select>
      <button v-on:click="showPopup=false">Close</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    rates: Object,
  },
  emits: ['getRates'],
  data: function() {
    return {
      tickets: ['USD', 'EUR', 'UAH'],
      currencies: ['USD', 'EUR', 'UAH','BTC','ETH'],
      toggle: 1,
      selectedTicket:'',
      showPopup: false,
      searchTerm:'',
      arrayRates: [],
      refreshButton: true,
      counter: 5,
    }
  },
  name: "TabloidArea",
  methods: {
    setTabloid: function(ticket){
      this.toggle = 1/this.rates[ticket];
    },
    setCurrency: function(ticket){
      return (this.rates[ticket] < 1) ? Math.round(this.rates[ticket]*this.toggle*1000000)/1000000: Math.round(this.rates[ticket]*this.toggle*100)/100;
    },
    setRatesToArray: function () {
      let that = this;
      for (const [key, value] of Object.entries(that.rates)) {
        that.arrayRates.push([key, value]);
      }
    },
    refreshRates: function (){
      this.$emit('getRates');
      let wait = setInterval(()=> {
        this.counter--;
        if (this.counter === 0) {
          clearInterval(wait);
          this.counter = 5;
        }
      }, 1000)
    },
  },
  mounted() {
    setTimeout(()=>this.setRatesToArray(),1000);
    if (localStorage.getItem('currenciesOnTabloid')) {
      this.currencies = localStorage.getItem('currenciesOnTabloid').split(',')
    }

  },
  watch: {
    currencies: {
      handler() {
          localStorage.setItem('currenciesOnTabloid', this.currencies)
        },
        deep: true
      }
    }
}
</script>

<style scoped>
  .popup {
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    align-self: center;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: grey;
    z-index:1;
  }
  .popup-button {
    padding: 20px;
    position: absolute;
    height: 200px;
    width: 400px;
    border-radius: 15px;
    background-color: aquamarine;
    z-index:2;
  }
</style>