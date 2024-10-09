<template>
  <div class="weather-app">
    <div class="card weather-form-card" :class="{ 'move-left': weather }">
      <div class="card-body">
        <h2 class="card-title text-center">Prepared During Interview</h2>
        <input type="text" placeholder="Enter Any Number" :maxlength="5" >
        <div class="form-group">
          <select v-model.trim="country" class="form-select mt-1" name="country" required @change="SelectedCountry">
            <option selected value="">Select Country</option>
            <option v-for="l in countryList" :key="l.country_name" :value="l.country_name" v-text="l.country_name" />
          </select>
          <select v-model.trim="state" class="form-select mt-1" name="state" required @change="SelectedCity">
            <option selected value="">Select State</option>
            <option v-for="l in stateList" :key="l.state_name" :value="l.state_name" v-text="l.state_name" />
          </select>
          <select v-model.trim="city" class="form-select mt-1" name="City" required>
            <option selected value="">Select City</option>
            <option v-for="l in cityList" :key="l.city_name" :value="l.city_name" v-text="l.city_name" />
          </select>
        </div>
        <div>
          { "country": "{{ country }}", "state": "{{ state }}", "city": "{{ city }}" }
        </div>
        <div v-if="error" class="alert alert-danger mt-3">{{ error }}</div>
      </div>
    </div>
    <div v-if="notification" class="notification">{{ notification }}</div>
  </div>
</template>

<script>
import axios from 'axios';


// navigator.geolocation.getCurrentPosition(
//   position => { console.log(`Lat:${position.coords.latitude}`, `Long:${position.coords.longitude}`); },
//    error => { console.error(error.message); });

// Notification.requestPermission().then( Permission=>{
//   new Notification('Hellow, World!');
// });
export default {
  data() {
    return {
      country: '',
      city: '',
      weather: null,
      error: '',
      loading: false,
      notification: '',
      errors: {},
      countryList: [],
      accesstoken: "",
      state: "",
      stateList: [],
      cityList: [],

    };
  },
  created() {
    this.getaccesstoken();

  },
  methods: {
    validateInput(field) {
      this.errors[field] = '';
      if (!this[field]) {
        this.errors[field] = `The ${field} field is required.`;
      }
    },

    async getaccesstoken() {
      // this.loading = true;
      // this.error = '';
      try {
        const response = await axios.get(
          `https://www.universal-tutorial.com/api/getaccesstoken`,
          {
            headers: {
              "Accept": "application/json",
              "api-token": "Bym360GEhG_f42nsmvxfOCHfu4atzdoWihSj9L5qxPoGUcw2YqsAxz87AQXmVzJca5U",
              "user-email": "abhishekkumarkyp@gmail.com"
            }
          }
        );


        if (response.data && response.data.auth_token) {
          // console.log(response.data.auth_token)
          this.accesstoken = response.data.auth_token;
          this.getCountryList();
        } else {
          this.error = 'No accesstoken found in the response.';
        }

      } catch (error) {
        this.error = 'Failed to fetch accesstoken. Please try again.';
      } finally {
        this.loading = false;
      }
    },
    async getCountryList() {
      // this.loading = true;
      // this.error = '';

      try {
        const response = await axios.get(
          `https://www.universal-tutorial.com/api/countries/`,
          {
            headers: {
              "Authorization": "Bearer " + this.accesstoken,
              "Accept": "application/json"
            }
          }
        );

        if (response.data && response.data.length > 0) {
          this.countryList = response.data;
        } else {
          this.error = 'No country found in the response.';
        }

      } catch (error) {
        this.error = 'Failed to fetch countries. Please try again.';
      } finally {
        this.loading = false;
      }
    },
    async SelectedCountry() {
      // this.loading = true;
      // this.error = '';

      try {
        const response = await axios.get(
          `https://www.universal-tutorial.com/api//states/${this.country}`,
          {
            headers: {
              "Authorization": "Bearer " + this.accesstoken,
              "Accept": "application/json"
            }
          }
        );

        if (response.data && response.data.length > 0) {
          this.stateList = response.data;
        } else {
          this.error = 'No state found in the response.';
        }
      } catch (error) {
        this.error = 'Failed to fetch state. Please try again.';
      } finally {
        this.loading = false;
      }
    },
    async SelectedCity() {
      // this.loading = true;
      // this.error = '';

      try {
        const response = await axios.get(
          `https://www.universal-tutorial.com/api/cities/${this.state}`,
          {
            headers: {
              "Authorization": "Bearer " + this.accesstoken,
              "Accept": "application/json"
            }
          }
        );


        if (response.data && response.data.length > 0) {
          this.cityList = response.data;
        } else {
          this.error = 'No city found in the response.';
        }
      } catch (error) {
        this.error = 'Failed to fetch city. Please try again.';
      } finally {
        this.loading = false;
      }
    },

    async getWeather() {
      this.validateInput('country');
      this.validateInput('city');
      if (this.errors.country || this.errors.city) return;

      this.loading = true;
      try {
        const apiKey = '2f75a68fbf9743e99e30a1b686448abf';
        // const response = await axios.get(
        //   `https://api.openweathermap.org/data/2.5/weather?q=${this.city},${this.country}&APPID=${apiKey}`
        // );
        const response = await axios.get(
          `https://www.universal-tutorial.com/rest-apis/free-rest-api-for-country-state-city?q=${this.city},${this.country}&APPID=${apiKey}`
        );
        this.weather = response.data;
        this.error = '';
        this.notification = 'Weather data fetched successfully!';
        setTimeout(() => (this.notification = ''), 3000);

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
