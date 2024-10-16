<template>
  <div class="hourly-forecast">
    <h2 class="text-center">Hourly Weather Forecast</h2>
    <div class="forecast-container">
      <div v-if="hourlyData.length">
        <div
          class="forecast-item"
          v-for="(hour, index) in hourlyData"
          :key="index"
        >
          <p>{{ formatTime(hour.dt) }}</p>
          <img :src="getWeatherIcon(hour.weather[0].main)" alt="Weather Icon" />
          <p>{{ hour.temp }} °C</p>
          <p>Humidity: {{ hour.humidity }}%</p>
          <p>Precipitation: {{ hour.pop * 100 }}%</p>
        </div>
      </div>
      <div v-else>
        <p>No hourly forecast data available.</p>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {
  props: {
    latitude: {
      type: Number,
      required: true,
    },
    longitude: {
      type: Number,
      required: true,
    },
  },
  setup(props) {
    const hourlyData = ref([]);

    // Function to fetch hourly weather data
    const fetchHourlyForecast = async () => {
      const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Your OpenWeatherMap API key
      // const delhi = { latitude:28.7041, longitude:77.1025};
      console.log(props);
      // const url = `https://api.openweathermap.org/data/2.5/hourly?lat=${props.latitude}&lon=${props.longitude}&appid=${apiKey}&units=metric`;
      // const url = `https://api.openweathermap.org/data/2.5/onecall?lat=${delhi.latitude}&lon=${delhi.longitude}&appid=${apiKey}`;
      const url = `https://pro.openweathermap.org/data/2.5/forecast/hourly?lat=44.34&lon=10.99&appid=${apiKey}`;

      // https://pro.openweathermap.org/data/2.5/forecast/hourly?lat={lat}&lon={lon}&appid={API key}
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error("Error fetching hourly data");
        const data = await response.json();
        hourlyData.value = data.hourly; // Set the hourly data
      } catch (error) {
        console.error(error);
      }
    };

    // Format timestamp into readable time
    const formatTime = (timestamp) => {
      const date = new Date(timestamp * 1000);
      return date.toLocaleTimeString([], {
        hour: "2-digit",
        minute: "2-digit",
      });
    };

    // Get weather icon based on condition
    const getWeatherIcon = (condition) => {
      const weatherIcons = {
        Clear: "https://openweathermap.org/img/wn/01d@2x.png",
        "Few Clouds": "https://openweathermap.org/img/wn/02d@2x.png",
        "Scattered Clouds": "https://openweathermap.org/img/wn/03d@2x.png",
        "Broken Clouds": "https://openweathermap.org/img/wn/04d@2x.png",
        "Shower Rain": "https://openweathermap.org/img/wn/09d@2x.png",
        Rain: "https://openweathermap.org/img/wn/10d@2x.png",
        Thunderstorm: "https://openweathermap.org/img/wn/11d@2x.png",
        Snow: "https://openweathermap.org/img/wn/13d@2x.png",
        Mist: "https://openweathermap.org/img/wn/50d@2x.png",
      };

      return weatherIcons[condition] || weatherIcons.Clear; // Default to Clear icon if condition not found
    };

    onMounted(() => {
      fetchHourlyForecast(); // Fetch data when the component is mounted
    });

    return {
      hourlyData,
      formatTime,
      getWeatherIcon,
    };
  },
};
</script>

<style scoped>
.hourly-forecast {
  max-width: 800px;
  margin: 0 auto;
  text-align: center;
}

.forecast-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

.forecast-item {
  margin: 10px;
  padding: 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
  width: 120px;
}
</style>
