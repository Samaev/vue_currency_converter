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
  <button>Refresh</button>
  <div v-if="showPopup" class="popup">
    <div class="popup-button">
      <input type="text" v-model="searchTerm" placeholder="Enter the ticker">

      <button v-on:click="showPopup=false">Close</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    rates: Object,
  },
  data: function() {
    return {
      tickets: ['USD', 'EUR', 'UAH'],
      currencies: ['USD', 'EUR', 'UAH','BTC','ETH'],
      toggle: 1,
      showPopup: false,
      searchTerm:'',
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
    opacity: 0.5;
  }
  .popup-button {
    position: absolute;
    height: 200px;
    width: 400px;
    border-radius: 15px;
    top: 30%;
    opacity:1;
    background-color: aquamarine;
  }
</style>