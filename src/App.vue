<template>
  <ConvertArea v-bind:rates="rates" />
  <TabloidArea v-bind:rates="rates" v-on:getRates="getRates()"/>
</template>

<script>
import ConvertArea from './components/Converter.vue'
import TabloidArea from "@/components/Tabloid";

export default {
  name: 'App',
  emits: ['getRates'],
  components: {
    ConvertArea,
    TabloidArea,
  },
  data: function (){
    return {
      rates: {},
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
    };
  },
},
  mounted() {
    this.getRates();
  }
}
</script>

<style>
#app {
  font-family: Arial, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
}
</style>
