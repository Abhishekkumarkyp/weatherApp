<template>
  <div class="container main-background">
    <!-- <nav class="navbar navbar-dark bg-dark ps-2">
  <a class="navbar-brand" href="#">Weather App</a>
</nav> -->
    <nav class="navbar ps-2" style="background-color: #1a1a4d">
      <!-- Changed to a custom background color -->
      <a class="navbar-brand text-white" href="#">Weather App</a>
      <!-- Added text-white for contrast -->
    </nav>

    <div class="scrollable-container">
      <!-- Your content goes here -->

      <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
      <WeatherInformation />
      <hr>
      <!-- <CurrentUsersLocWeather/> -->
      <Day5Forecast />
      <hr>
      <AirQuality />
      <Air_Polustion_Concept />
      <hr>
      <WeatherMap />
      <!-- <HourlyForecast /> -->
      <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import WeatherInformation from "./components/WeatherInformation.vue";
// import CurrentUsersLocWeather from './components/CurrentUsersLocWeather.vue'
import Day5Forecast from "./components/Day5Forecast.vue";
import WeatherMap from "./components/WeatherMap.vue";
// import HourlyForecast from "./components/HourlyForecast.vue";
import AirQuality from "./components/AirQuality.vue";
import Air_Polustion_Concept from "./components/Air_Polustion_Concept.vue";

export default {
  name: "App",
  components: {
    // HelloWorld,
    WeatherInformation,
    // CurrentUsersLocWeather,
    Day5Forecast,
    WeatherMap,
    // HourlyForecast,
    AirQuality,
    Air_Polustion_Concept,
  },
};
</script>

<style>
.scrollable-container {
  height: 100%; /* Set the desired height */
  overflow-y: auto; /* Enable vertical scrolling */
  /* border: 1px solid #ddd; Optional: Add a border for visual clarity */
  padding: 10px; /* Optional: Add some padding */
}

body {
  background: linear-gradient(
    135deg,
    #6e7cfa,
    #a4c8e1
  ); /* Adjust colors as needed */
}
</style>
