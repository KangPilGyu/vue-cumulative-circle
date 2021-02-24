<template>
  <div id="app">
    <cumulative-circle :size="200"
                      :stroke-width="8"
                      v-model="data"/>
    <div>
      <p>data</p>
      <div><label>data 0</label><input v-model="data[0].percentage"></div>
      <div><label>data 1</label><input v-model="data[1].percentage"></div>
      <div><label>data 2</label><input v-model="data[2].percentage"></div>
      <div><label>data 3</label><input v-model="data[3].percentage"></div>
    </div>
  </div>
</template>

<script>
import CumulativeCircle from "./CumulativeCircle";
export default {
  name: 'App',
  components: {
    CumulativeCircle
  },
  data() {
    return {
      data: [
        {
          color: '#ffa10e',
          percentage: 10
        },
        {
          color: '#1886ff',
          percentage: 20
        },
        {
          color: 'red',
          percentage: 20
        },
        {
          color: 'blue',
          percentage: 10
        }
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
label {
  margin-right: 10px;
}
</style>
