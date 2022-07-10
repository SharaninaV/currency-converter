<template>
  <div id="app">
  <ui-grid>
    <ui-grid-cell columns="12">
      <h1 :class="$tt('headline2')">Currency Converter</h1>
      <CurrencyConverter />
    </ui-grid-cell>
  </ui-grid>
  </div>
</template>

<script>
import CurrencyConverter from '@/components/CurrencyConverter'
export default {
  name: 'App',
  components: {
    CurrencyConverter
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  margin: 10% auto;
  background: white;
  border-radius: 10px;
  width: 60%;
}
html {
  background: url('https://swall.teahub.io/photos/small/96-961759_white-and-green-gradient-background.jpg') no-repeat;
  background-size: cover;
  height: 100%;
  text-align: center;
}
h1 {
  color: #017556;
}
</style>
