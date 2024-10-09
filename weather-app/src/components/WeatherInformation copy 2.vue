<template>
  <div class="weather-app">
    <div class="card weather-form-card" :class="{ 'move-left': weather }">
      <div class="card-body">
        <h2 class="card-title text-center">Weather App</h2>
        <div class="form-group">
          <input type="text" class="form-control" v-model="country" placeholder="Enter country"
            @input="validateInput('country')" />
          <div v-if="errors.country" class="text-danger">{{ errors.country }}</div>
        </div>
        <div class="form-group mt-3">
          <input type="text" class="form-control" v-model="city" placeholder="Enter city name"
            @input="validateInput('city')" />
          <div v-if="errors.city" class="text-danger">{{ errors.city }}</div>
        </div>
        <button class="btn btn-primary mt-3" :disabled="loading" @click="getWeather">
          <span v-if="loading" class="spinner-border spinner-border-sm"></span>
          <span v-if="!loading">Get Weather</span>
        </button>
        <div v-if="error" class="alert alert-danger mt-3">{{ error }}</div>
      </div>
    </div>
    <div class="weather-info" v-if="weather" :class="{ 'move-in': weather }">
      <h3>Weather in {{ weather.name }}</h3>
      <p><strong>Temperature:</strong> {{ weather.main.temp }} °C</p>
      <p><strong>Description:</strong> {{ weather.weather[0].description }}</p>
      <p><strong>Humidity:</strong> {{ weather.main.humidity }} %</p>
      <p><strong>Wind Speed:</strong> {{ weather.wind.speed }} m/s</p>
    </div>
    <div v-if="notification" class="notification">{{ notification }}</div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      country: '',
      city: '',
      weather: null,
      error: '',
      loading: false,
      notification: '',
      errors: {}
    };
  },
  methods: {
    // validateInput(field) {
    //   this.errors[field] = '';
    //   if (!this[field]) {
    //     this.errors[field] = `The ${field} field is required.`;
    //   }
    // },
  validateInput(field) {
    this.errors[field] = '';
    if (!this[field]) {
      this.errors[field] = `The ${field} field is required.`;
    }
  },
  async getWeather() {
    this.validateInput('country');
    this.validateInput('city');
    if (this.errors.country || this.errors.city) return;

    this.loading = true;
    try {
      const apiKey = '2f75a68fbf9743e99e30a1b686448abf';
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?q=${this.city},${this.country}&APPID=${apiKey}`
          );
      this.weather = response.data;
      this.error = '';
      this.notification = 'Weather data fetched successfully!';
      setTimeout(() => this.notification = '', 3000);

      // Update background color based on weather
      const weatherCondition = this.weather.weather[0].main.toLowerCase();
      const appElement = document.querySelector('.weather-app');
      if (weatherCondition.includes('rain')) {
        appElement.style.background = 'linear-gradient(135deg, #4b79a1, #283e51)';
      } else if (weatherCondition.includes('clear')) {
        appElement.style.background = 'linear-gradient(135deg, #ff7e5f, #feb47b)';
      } else if (weatherCondition.includes('clouds')) {
        appElement.style.background = 'linear-gradient(135deg, #bdc3c7, #2c3e50)';
      } else if (weatherCondition.includes('snow')) {
        appElement.style.background = 'linear-gradient(135deg, #83a4d4, #b6fbff)';
      } else {
        appElement.style.background = 'linear-gradient(135deg, #1e90ff, #ff6347)';
      }
    } catch (err) {
      this.error = 'Failed to fetch weather data. Please try again.';
      this.weather = null;
    } finally {
      this.loading = false;
    }
  },
    async getWeatherg() {
      this.validateInput('country');
      this.validateInput('city');
      if (this.errors.country || this.errors.city) return;

      this.loading = true;
      try {
        const apiKey = '2f75a68fbf9743e99e30a1b686448abf';
        const response = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city},${this.country}&APPID=${apiKey}`
        );
        this.weather = response.data;
        this.error = '';
        this.notification = 'Weather data fetched successfully!';
        setTimeout(() => this.notification = '', 3000);
      } catch (err) {
        this.error = 'Failed to fetch weather data. Please try again.';
        this.weather = null;
      } finally {
        this.loading = false;
      }
    }
  }
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
}

.weather-form-card {
  width: 300px;
  margin-right: auto;
  transition: transform 0.5s ease-in-out;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.weather-form-card.move-left {
  transform: translateX(-200px);
}

.weather-info {
  max-width: 400px;
  background: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  opacity: 0;
  transition: opacity 0.5s ease-in-out, transform 0.5s ease-in-out;
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
</style>
