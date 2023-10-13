<script setup>
import side_bar from './side_bar.vue';
import { countries } from './side_bar.vue'
import VueWorldMap from "vue-world-map";
import { ref } from 'vue';

const map = ref({
  defaultCountryFillColor: "#663300",
  highColor: "red",
  lowColor: "red",
  countryStrokeColor: "black",
});
</script>

<template>
  <div class="row">
    <div class="sidebar">
      <side_bar></side_bar>
    </div>
    <div class="map">
      <vue-world-map id="worldmap" v-bind:countryData="countries"
        v-bind:defaultCountryFillColor="map.defaultCountryFillColor" v-bind:highColor="map.highColor"
        v-bind:countryStrokeColor="map.countryStrokeColor" v-bind:lowColor="map.lowColor"></vue-world-map>
    </div>
  </div>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.map {
  float: left;
  width: 80%;
}

.sidebar {
  float: left;
  width: 20%;
}

.row:after {
  content: "";
  display: table;
  clear: both;
}
</style>
