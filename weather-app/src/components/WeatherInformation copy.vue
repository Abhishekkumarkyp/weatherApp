<template>
  <div class="weather-app container mt-5">
    <h1 class="text-center">Weather App</h1>
    <div class="form-group">
      <input
        type="text"
        class="form-control"
        v-model="country"
        placeholder="Enter Country Name"
      />
    </div>

    <div class="form-group mt-2">
      <input
        type="text"
        class="form-control"
        v-model="city"
        placeholder="Enter city name"
      />
    </div>
    <button class="btn btn-primary mt-3" @click="getWeather">
      Get Weather
    </button>

    <div v-if="error" class="alert alert-danger mt-3">{{ error }}</div>

    <div v-if="weather" class="mt-5">
      <h3>Weather in {{ weather.name }}</h3>
      <p><strong>Temperature:</strong> {{ weather.main.temp }} °C</p>
      <p><strong>Description:</strong> {{ weather.weather[0].description }}</p>
      <p><strong>Humidity:</strong> {{ weather.main.humidity }} %</p>
      <p><strong>Wind Speed:</strong> {{ weather.wind.speed }} m/s</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      country: "",
      city: "",
      weather: null,
      error: "",
      // lat: '28.7041',
      // lon: '77.1025',
    };
  },
  methods: {
    async getWeather() {
      try {
        // const response = await axios.get(
        //   `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`
        // `https://api.openweathermap.org/data/2.5/weather?q=${this.city},uk&APPID=${apiKey}`

        // );
        // `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`
        const apiKey = "2f75a68fbf9743e99e30a1b686448abf";
        const response = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city},${this.country}&APPID=${apiKey}`
        );
        this.weather = response.data;
        this.error = "";
      } catch (err) {
        this.error = "Failed to fetch weather data. Please try again.";
        this.weather = null;
      }
    },
  },
};
</script>

<style scoped>
.weather-app {
  max-width: 600px;
  margin: auto;
}
</style>
