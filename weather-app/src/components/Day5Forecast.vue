<template>
  <div class="forecast container mt-5">
    <div v-if="NextThreeHousForcast">
        <h2 class="text-center">
          Next Three Hourly Weather Forecast: {{ cityInput }}
        </h2>
        <div class="row">
          <div
            v-for="day in NextThreeHousForcast"
            :key="day.dt"
            class="col-md-4 text-center mb-3"
          >
            <!-- {{ day }} -->
            <div class="forecast-card border p-3">
              <h5>{{ formatDate(day.dt) }}</h5>
              <p><strong>Temperature:</strong> {{ day.main.temp }} °C</p>
              <p><strong>Condition:</strong> {{ day.weather[0].description }}</p>
              <p><strong>Humidity:</strong> {{ day.main.humidity }}%</p>
              <img
                :src="getWeatherIcon(day.weather[0].icon)"
                alt="Weather Icon"
              />
            </div>
          </div>
        </div>
      </div>
    <h2 class="text-center">
      Next Five Days Weather Forecast for : {{ cityInput }}
    </h2>
    <div class="row">
      <div class="col-8">
        <input
          v-model="cityInput"
          @keyup.enter="fetchForecastByCity"
          placeholder="Enter city name for forecast"
          class="form-control mb-3"
        />
      </div>
      <div class="col-4">
        <button @click="fetchForecastByCity" class="btn btn-primary w-100">
          Get 5-Day Forecast
        </button>
      </div>
    </div>
    <div v-if="loading" class="text-center">Loading...</div>
    <div v-else-if="error" class="alert alert-danger">{{ error }}</div>
    <div v-else>
      <div class="row">
        <div
          v-for="day in forecast"
          :key="day.dt"
          class="col-md-4 text-center mb-3"
        >
          <!-- {{ day }} -->
          <div class="forecast-card border p-3">
            <h5>{{ formatDate(day.dt) }}</h5>
            <p><strong>Temperature:</strong> {{ day.main.temp }} °C</p>
            <p><strong>Condition:</strong> {{ day.weather[0].description }}</p>
            <p><strong>Humidity:</strong> {{ day.main.humidity }}%</p>
            <img
              :src="getWeatherIcon(day.weather[0].icon)"
              alt="Weather Icon"
            />
          </div>
        </div>
      </div>
      
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      forecast: [],
      NextThreeHousForcast: [],
      cityInput: "Delhi",
      loading: false,
      error: null,
    };
  },
  created() {
    this.fetchForecastByCity();
  },
  methods: {
    async fetchForecastByCity() {
      if (!this.cityInput) {
        this.error = "Please enter a city name";
        return;
      }

      this.loading = true;
      this.error = null;
      const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Access the API key from .env
      const url = `https://api.openweathermap.org/data/2.5/forecast?q=${this.cityInput}&appid=${apiKey}&units=metric`;

      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error("City not found");
        }
        const data = await response.json();
        this.updateForecastData(data);
      } catch (err) {
        this.error = err.message;
      } finally {
        this.loading = false;
        // this.cityInput = ''; // Clear the input after fetching
      }
    },

    updateForecastData(data) {
      // Filter the forecast data to show one entry per day
      // this.forecast = data.list;
      this.forecast = data.list.filter((item) =>
        item.dt_txt.includes("12:00:00")
      );

      const today = new Date();
      const todayString = today.toISOString().split("T")[0]; // Get today's date in 'YYYY-MM-DD' format

      this.NextThreeHousForcast = data.list.filter((item) => {
        return (
          item.dt_txt.startsWith(todayString) &&
          item.dt_txt.includes("12:00:00")
        );
      });
    },

    // formatDate(timestamp) {
    //   const date = new Date(timestamp * 1000);
    //   return date.toLocaleDateString('en-US', { weekday: 'long', month: 'short', day: 'numeric' });
    // },
    formatDate(timestamp) {
      const date = new Date(timestamp * 1000);
      return date.toLocaleString("en-US", {
        weekday: "long",
        month: "short",
        day: "numeric",
        hour: "2-digit",
        minute: "2-digit",
        hour12: true, // Use 'true' for 12-hour format, 'false' for 24-hour format
      });
    },

    getWeatherIcon(iconCode) {
      return `https://openweathermap.org/img/wn/${iconCode}@2x.png`; // URL to the weather icon
    },
  },
};
</script>

<style scoped>
.forecast {
  max-width: 800px;
  margin: 0 auto;
}

.forecast-card {
  border-radius: 8px;
  background-color: #f8f9fa;
}
</style>
