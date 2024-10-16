<template>
  <div class="weather-app rounded">
    <div class="card weather-form-card" :class="{ 'move-left': weather }">
      <div class="card-body">
        <h2 class="card-title text-center">Location</h2>
        <!-- <div class="form-group">
          <input
            type="text"
            class="form-control"
            v-model="country"
            placeholder="Enter country"
            @input="validateInput('country')"
          />
          <div v-if="errors.country" class="text-danger">{{ errors.country }}</div>
        </div> -->
        <div class="form-group mt-3">
          <input
            type="text"
            class="form-control"
            v-model="city"
            placeholder="Enter city name"
            @input="validateInput('city')"
          />
          <div v-if="errors.city" class="text-danger">{{ errors.city }}</div>
        </div>
        <button
          class="btn btn-primary mt-3"
          :disabled="loading"
          @click="getWeather"
        >
          <span v-if="loading" class="spinner-border spinner-border-sm"></span>
          <span v-if="!loading">Get Weather</span>
        </button>
        <div v-if="error" class="alert alert-danger mt-3">{{ error }}</div>
      </div>
    </div>
    <div class="weather-info" v-if="weather" :class="{ 'move-in': weather }">
      <h3>Current Weather in {{ weather.name }}</h3>
      <p>
        <strong>Temperature:</strong>
        {{ (weather.main.temp - 273).toFixed(2) }} °C
      </p>
      <p><strong>Description:</strong> {{ weather.weather[0].description }}</p>
      <p><strong>Humidity:</strong> {{ weather.main.humidity }} %</p>
      <p><strong>Wind Speed:</strong> {{ weather.wind.speed }} m/s</p>
    </div>
    <div v-if="notification" class="notification">{{ notification }}</div>
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
      loading: false,
      notification: "",
      errors: {},
    };
  },
  async created() {
    await this.fetchWeatherByLocation(); // Automatically fetch weather on component mount
    //  await this.getWeather(); // Automatically fetch weather on component mount
  },
  methods: {
    validateInput(field) {
      this.errors[field] = "";
      if (!this[field]) {
        this.errors[field] = `The ${field} field is required.`;
      }
    },
    async getWeather() {
      // this.validateInput('country');
      this.validateInput("city");
      if (this.errors.country || this.errors.city) return;

      this.loading = true;
      try {
        const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY;
        const response = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city},${this.country}&APPID=${apiKey}`
        );
        this.weather = response.data;
        this.error = "";
        this.notification = "Weather data fetched successfully!";
        setTimeout(() => (this.notification = ""), 3000);

        // Update background color based on weather
        const weatherCondition = this.weather.weather[0].main.toLowerCase();
        const appElement = document.querySelector(".weather-app");
        if (weatherCondition.includes("rain")) {
          appElement.style.background =
            "linear-gradient(135deg, #4b79a1, #283e51)";
        } else if (weatherCondition.includes("clear")) {
          appElement.style.background =
            "linear-gradient(135deg, #ff7e5f, #feb47b)";
        } else if (weatherCondition.includes("clouds")) {
          appElement.style.background =
            "linear-gradient(135deg, #bdc3c7, #2c3e50)";
        } else if (weatherCondition.includes("snow")) {
          appElement.style.background =
            "linear-gradient(135deg, #83a4d4, #b6fbff)";
        } else {
          appElement.style.background =
            "linear-gradient(135deg, #1e90ff, #ff6347)";
        }
      } catch (err) {
        this.error = "Failed to fetch weather data. Please try again.";
        this.weather = null;
      } finally {
        this.loading = false;
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
      this.getWeather();
    },
  },
};
</script>

<style scoped>
.weather-app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(135deg, #1e90ff, #ff6347);
  transition: background-color 0.5s ease;
  padding: 20px;
  position: relative;
}

.weather-form-card {
  width: 400px;
  padding: 20px;
  margin: auto;
  transition: transform 0.5s ease-in-out;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  animation: fadeIn 0.5s ease-in-out;
}

.weather-form-card.move-left {
  transform: translateX(-200px);
}

.weather-info {
  width: 400px;
  padding: 20px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  opacity: 0;
  transition: opacity 0.5s ease-in-out, transform 0.5s ease-in-out;
  animation: fadeIn 0.5s ease-in-out;
}

.weather-info.move-in {
  opacity: 1;
  transform: translateX(0);
}

.notification {
  position: fixed;
  top: 20px;
  right: 20px;
  background: #28a745;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  animation: fadeInOut 3s ease-in-out;
}

@keyframes fadeInOut {
  0% {
    opacity: 0;
  }
  10% {
    opacity: 1;
  }
  90% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
