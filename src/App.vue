<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <ConvertArea v-bind:rates="rates"/>
  <TabloidArea v-bind:rates="rates"/>
</template>

<script>
import ConvertArea from './components/Converter.vue'
import TabloidArea from "@/components/Tabloid";

export default {
  name: 'App',
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
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
