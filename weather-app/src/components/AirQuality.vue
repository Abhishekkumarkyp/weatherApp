<template>
  <div class="air-quality">
    <h2 class="text-center">Air Quality Index</h2>
    <div class="row">
      <div class="col-9">
        <input
          v-model="latitude"
          @keyup.enter="fetchAirQuality"
          placeholder="Enter Latitude"
          class="form-control mb-3"
        />
        <input
        v-model="longitude"
        @keyup.enter="fetchAirQuality"
        placeholder="Enter Longitude"
        class="form-control mb-3"
        />
      </div>
      
      <div class="col-3">
        <button @click="fetchAirQuality" class="btn btn-primary w-100">
          Get Air Quality
        </button>
      </div>
    </div>

    <!-- <div v-if="aqiData">
      <h3>Location: {{ aqiData.city }}</h3>
      <p>AQI: {{ aqiData.aqi }}</p>
      <p>Quality: {{ aqiData.quality }}</p>
      <p>Pollutant: {{ aqiData.primaryPollutant }}</p>
    </div>
    <div v-else>
      <p>No air quality data available.</p>
    </div> -->

    <div class="container">
    <h1 class="text-center my-4">Air Quality Index (AQI)</h1>
    <div class="card" v-if="aqiData">
      <div class="card-body">
        <h5 class="card-title">AQI Details</h5>
        <p class="card-text"><strong>AQI:</strong> {{ aqiData.aqi }}</p>
        <p class="card-text"><strong>Quality:</strong> {{ aqiData.quality }}</p>
        <p class="card-text"><strong>Primary Pollutant:</strong> {{ aqiData.primaryPollutant }}</p>
        <p class="card-text"><strong>Pollutant Components:</strong></p>
        <ul>
          <li v-for="(value, key) in aqiData.components" :key="key">
            {{ key }}: {{ value }}
          </li>
        </ul>
      </div>
    </div>
    <div v-else>
      <div class="alert alert-warning text-center mt-4">
        No AQI data available.
      </div>
    </div>
  </div>
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  setup() {
    const cityInput = ref("Delhi"); // Default city
    const aqiData = ref(null); // Store AQI data
    const error = ref({}); // Store AQI data
    const loading = ref(''); // Store AQI data
    const latitude = ref(''); // Store AQI data
    const longitude = ref(''); // Store AQI data

  


    const getAQIQuality =(aqi) => {
      // Function to determine air quality category based on AQI value
      if (aqi <= 50) return "Good";
      if (aqi <= 100) return "Moderate";
      if (aqi <= 150) return "Unhealthy for Sensitive Groups";
      if (aqi <= 200) return "Unhealthy";
      if (aqi <= 300) return "Very Unhealthy";
      return "Hazardous";
    };
    const getPrimaryPollutant=(components)=>  {
      // Function to determine the primary pollutant
      const pollutants = Object.keys(components);
      const primaryPollutant = pollutants.reduce((prev, curr) => {
        return components[curr] > components[prev] ? curr : prev;
      });
      return primaryPollutant;
    };

    const fetchAirQuality = async () => {
      error.value = '';
      if (!navigator.geolocation) {
        error.value = "Geolocation is not supported by this browser.";
        return;
      }

      loading.value = true;
      error.value = null;

      navigator.geolocation.getCurrentPosition(
        async (position) => {
          latitude.value = position.coords.latitude;
          longitude.value = position.coords.longitude;
         const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Access the API key from .env
          // const url = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${apiKey}&units=metric`;

          const  url  =`http://api.openweathermap.org/data/2.5/air_pollution?lat=${latitude.value}&lon=${longitude.value}&appid=${apiKey}`;
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error("Error fetching AQI data");
        const data = await response.json();

        // Extract relevant AQI data
        if (data.list.length > 0) {
          const measurement = data.list[0]; // Get the first measurement
          aqiData.value = {
            aqi: measurement.main.aqi,
            quality: getAQIQuality(measurement.main.aqi),
            primaryPollutant: getPrimaryPollutant(measurement.components),
            components: measurement.components // Include detailed components if needed
          };
        } else {
          console.error("No AQI data found");
          aqiData.value = null; // Set to null if no data found
        }
      } catch (error) {
        console.error(error);
      }finally {
            loading.value = false;
          }
        },
        (error) => {
          loading.value = false;
          error.value = "Unable to retrieve your location: " + error.message;
        }
      );
    };


    return {
      cityInput,
      fetchAirQuality,
      aqiData,
      getAQIQuality,
      getPrimaryPollutant,
      loading,
      error,
      latitude,
      longitude,
    };
  },
};
</script>

<style scoped>
.air-quality {
  max-width: 600px;
  margin: 0 auto;
  text-align: center;
}
</style>
