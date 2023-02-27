<template>
<div class="card m-auto mb-5" style="width: 18rem;">
  <div class="card-header">
    <h4>Currency tabloid</h4>
  </div>

  <div>
    <button class="btn btn-outline-primary m-2" v-for="(ticket, index) in tickets" v-bind:key="index" v-on:click="setTabloid(ticket)">{{ ticket }}</button>
  </div>
  <div>

    <div class="list-group">
      <div v-for="(currency,index) in currencies" v-bind:key="index" class="list-group-item list-group-item-info m-1">
        <span>{{ currency }}</span>
        <span>&#8594;</span>
        <span>{{ setCurrency(currency) }}</span></div>
    </div>
    <div v-if="errorAdding">
      <div class="spinner-grow text-primary" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
      It was already in a list.
      <div class="spinner-grow text-primary" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
    <button class="btn btn-primary m-2" v-on:click="showPopup = true">Add currency</button>
  </div>
  <div><button class="btn btn-primary m-2" v-on:click="refreshRates" v-if="counter === 5">Refresh</button></div>
  <div v-if="counter !== 5">
    <div class="spinner-border text-primary" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
    <p>Currencies can be updated in {{ counter }} seconds. Please wait</p>
  </div>
  <div v-if="showPopup" class="popup p-5">
    <div class="card popup-button">
      <div class="card-title ">Add new currency</div>
      <input class="form-control m-2" type="text" v-model="searchTerm" placeholder="Enter the ticker">
      <select class="form-select multiple m-2" v-if="searchTerm.length > 0" multiple v-on:click="addCurrency" v-model="selectedTicket">
        <option
            v-for="(ticket,index) in arrayRates.filter(tick=>tick[0].toLowerCase().includes(searchTerm))"
            v-bind:key="index"
            v-bind:value="ticket[0]"
        >
          {{ ticket[0] }}
        </option>
      </select>
      <button class="btn btn-primary m-2" v-on:click="showPopup=false">Close</button>
    </div>
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
      errorAdding: false,
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
    addCurrency: function(){
      if (this.currencies.includes(this.selectedTicket[0])) {
        this.showPopup = false;
        this.errorAdding = true;
        setTimeout(()=>{
          this.errorAdding = false
        }, 2500)

        return;
      } else {
        this.currencies.push(this.selectedTicket[0])
      }
        this.showPopup = false;
    }
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
    height: max-content;
    width: 400px;
    border-radius: 15px;
    z-index:2;
  }
</style>