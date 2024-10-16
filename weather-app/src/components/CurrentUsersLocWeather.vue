<template>
  <div class="current-weather container mt-5">
    <h2 class="text-center">Current Weather</h2>
    <div v-if="loading" class="text-center">Loading...</div>
    <div v-else-if="error" class="alert alert-danger">{{ error }}</div>
    <div v-else>
      <div class="weather-info text-center">
        <h3>{{ city }}</h3>
        <p>
          Temperature: <strong>{{ temperature }} °C</strong>
        </p>
        <p>
          Condition: <strong>{{ weatherCondition }}</strong>
        </p>
        <p>
          Humidity: <strong>{{ humidity }}%</strong>
        </p>
        <p>
          Wind Speed: <strong>{{ windSpeed }} m/s</strong>
        </p>
      </div>
    </div>
    <div class="input-group mb-3">
      <input
        v-model="cityInput"
        @keyup.enter="fetchWeatherByCity"
        placeholder="Enter city name"
        class="form-control"
      />
      <button @click="fetchWeatherByCity" class="btn btn-primary">
        Get Weather
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      city: "",
      temperature: null,
      weatherCondition: "",
      humidity: null,
      windSpeed: null,
      cityInput: "",
      loading: false,
      error: null,
    };
  },
  created() {
    this.fetchWeatherByLocation(); // Automatically fetch weather on component mount
  },
  methods: {
    async fetchWeatherByCity() {
      if (!this.cityInput) {
        this.error = "Please enter a city name";
        return;
      }

      this.loading = true;
      this.error = null;
      const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Access the API key from .env
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.cityInput}&appid=${apiKey}&units=metric`;

      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error("City not found");
        }
        const data = await response.json();
        this.updateWeatherData(data);
      } catch (err) {
        this.error = err.message;
      } finally {
        this.loading = false;
        this.cityInput = ""; // Clear the input after fetching
      }
    },

    async fetchWeatherByLocation() {
      if (!navigator.geolocation) {
        this.error = "Geolocation is not supported by this browser.";
        return;
      }

      this.loading = true;
      this.error = null;

      navigator.geolocation.getCurrentPosition(
        async (position) => {
          const { latitude, longitude } = position.coords;
          const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Access the API key from .env
          const url = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${apiKey}&units=metric`;

          try {
            const response = await fetch(url);
            if (!response.ok) {
              throw new Error("Unable to fetch weather data");
            }
            const data = await response.json();
            this.updateWeatherData(data);
          } catch (err) {
            this.error = err.message;
          } finally {
            this.loading = false;
          }
        },
        (error) => {
          this.loading = false;
          this.error = "Unable to retrieve your location: " + error.message;
        }
      );
    },

    updateWeatherData(data) {
      this.city = data.name;
      this.temperature = data.main.temp;
      this.weatherCondition = data.weather[0].description;
      this.humidity = data.main.humidity;
      this.windSpeed = data.wind.speed;
    },
  },
};
</script>

<style scoped>
.current-weather {
  max-width: 600px;
  margin: 0 auto;
}

.weather-info {
  margin: 15px 0;
}
</style>
