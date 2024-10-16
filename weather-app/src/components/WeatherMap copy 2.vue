<template>
  <div class="weather-map">
    <h2 class="text-center">Weather Map</h2>
    <div id="map" style="height: 500px"></div>
    <input
      v-model="cityInput"
      @keyup.enter="fetchWeatherData"
      placeholder="Enter city name"
      class="form-control mb-3"
    />
    <button @click="fetchWeatherData" class="btn btn-primary">
      Get Weather
    </button>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import L from "leaflet";

export default {
  setup() {
    const cityInput = ref("Delhi");
    const map = ref(null);

    const marker = ref(null);
    const stateMarkers = ref([]);
    const isRequestInProgress = ref(false);

    const stateCoordinates = {
      "Andhra Pradesh": [15.9129, 79.74],
      "Arunachal Pradesh": [28.218, 94.7278],
      Assam: [26.2006, 92.9376],
      Bihar: [25.0961, 85.3131],
      Chhattisgarh: [21.2787, 81.8661],
      Goa: [15.2993, 74.124],
      Gujarat: [22.2587, 71.1924],
      Haryana: [29.0588, 76.0856],
      "Himachal Pradesh": [31.1048, 77.1734],
      Jharkhand: [23.6102, 85.2799],
      Karnataka: [15.3173, 75.7139],
      Kerala: [10.8505, 76.2711],
      "Madhya Pradesh": [22.9734, 78.6569],
      Maharashtra: [19.6633, 75.3698],
      Manipur: [24.6638, 93.9063],
      Meghalaya: [25.467, 91.3662],
      Mizoram: [23.1645, 92.9376],
      Nagaland: [26.1584, 94.5624],
      Odisha: [20.9517, 85.0985],
      Punjab: [30.7333, 76.7794],
      Rajasthan: [27.0238, 74.2176],
      Sikkim: [27.533, 88.5122],
      "Tamil Nadu": [11.1271, 78.6569],
      Telangana: [17.384, 78.4569],
      Tripura: [23.9408, 91.9882],
      "Uttar Pradesh": [26.8467, 80.9462],
      Uttarakhand: [30.0668, 79.0193],
      "West Bengal": [22.9868, 87.855],
    };

    // Weather Icons Object
    const weatherIcons = {
      clear: {
        day: "https://openweathermap.org/img/wn/01d@2x.png", // Clear Sky (Day)
        night: "https://openweathermap.org/img/wn/01n@2x.png", // Clear Sky (Night)
      },
      fewClouds: {
        day: "https://openweathermap.org/img/wn/02d@2x.png", // Few Clouds (Day)
        night: "https://openweathermap.org/img/wn/02n@2x.png", // Few Clouds (Night)
      },
      scatteredClouds: "https://openweathermap.org/img/wn/03d@2x.png", // Scattered Clouds
      brokenClouds: "https://openweathermap.org/img/wn/04d@2x.png", // Broken Clouds
      showerRain: "https://openweathermap.org/img/wn/09d@2x.png", // Shower Rain
      rain: "https://openweathermap.org/img/wn/10d@2x.png", // Rain
      thunderstorm: "https://openweathermap.org/img/wn/11d@2x.png", // Thunderstorm
      snow: "https://openweathermap.org/img/wn/13d@2x.png", // Snow
      mist: "https://openweathermap.org/img/wn/50d@2x.png", // Mist
    };

    // Initialize the map
    const initializeMap = () => {
      map.value = L.map("map").setView([20.5937, 78.9629], 5); // Center the map on India
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        maxZoom: 19,
      }).addTo(map.value);
    };

    // Add state markers to the map
    const addStateMarkers = () => {
      Object.entries(stateCoordinates).forEach(([state, coords]) => {
        console.log(state);
        const markerLayer = L.marker(coords, {
          icon: L.icon({
            iconUrl: "https://openweathermap.org/img/wn/01d@2x.png", // Default state marker icon
            iconSize: [35, 35],
            iconAnchor: [20, 30],
          }),
        }).addTo(map.value);

        stateMarkers.value.push(markerLayer); // Store marker in the reactive array
      });
    };

    // Fetch weather data for all states
    const fetchWeatherDataForAllStates = async () => {
      const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Your OpenWeatherMap API key
      const weatherPromises = Object.keys(stateCoordinates).map(
        async (state) => {
          const capitalCity = getCapitalCityForState(state); // Get the capital city
          const url = `https://api.openweathermap.org/data/2.5/weather?q=${capitalCity}&appid=${apiKey}&units=metric`;

          try {
            const response = await fetch(url);
            if (!response.ok)
              throw new Error("Weather not found for " + capitalCity);
            return response.json(); // Return the weather data
          } catch (error) {
            console.error(error);
            return null; // Return null for any failed requests
          }
        }
      );

      const weatherData = await Promise.all(weatherPromises); // Wait for all weather data to be fetched

      weatherData.forEach((data, index) => {
        if (data) {
          const state = Object.keys(stateCoordinates)[index];
          const coords = stateCoordinates[state];

          // Determine the appropriate icon based on weather conditions
          const condition = data.weather[0].main.toLowerCase(); // e.g., "Clear", "Rain", etc.
          let iconUrl;

          // Assign the correct icon URL based on the weather condition
          if (condition === "clear") {
            iconUrl = weatherIcons.clear.day; // Use the day icon (you can add logic for night)
          } else if (condition === "few clouds") {
            iconUrl = weatherIcons.fewClouds.day;
          } else if (condition === "scattered clouds") {
            iconUrl = weatherIcons.scatteredClouds;
          } else if (condition === "broken clouds") {
            iconUrl = weatherIcons.brokenClouds;
          } else if (condition === "shower rain") {
            iconUrl = weatherIcons.showerRain;
          } else if (condition === "rain") {
            iconUrl = weatherIcons.rain;
          } else if (condition === "thunderstorm") {
            iconUrl = weatherIcons.thunderstorm;
          } else if (condition === "snow") {
            iconUrl = weatherIcons.snow;
          } else if (condition === "mist") {
            iconUrl = weatherIcons.mist;
          } else {
            iconUrl = weatherIcons.clear.day; // Default to clear icon if unknown
          }

          // Add marker with weather info if data is available
          L.marker(coords, {
            icon: L.icon({
              iconUrl: iconUrl,
              iconSize: [25, 25],
              iconAnchor: [12.5, 25],
            }),
          })
            .addTo(map.value)
            .bindPopup(
              `${state}: Temperature ${data.main.temp}  <img src="${iconUrl}" alt="Weather Icon" /> °C<br>Condition: ${data.weather[0].description}`
            );
        }
      });
    };

    // Get the capital city for a given state
    const getCapitalCityForState = (state) => {
      const stateCapitals = {
        "Andhra Pradesh": "Amaravati",
        "Arunachal Pradesh": "Itanagar",
        Assam: "Dispur",
        Bihar: "Patna",
        Chhattisgarh: "Raipur",
        Goa: "Panaji",
        Gujarat: "Gandhinagar",
        Haryana: "Chandigarh",
        "Himachal Pradesh": "Shimla",
        Jharkhand: "Ranchi",
        Karnataka: "Bengaluru",
        Kerala: "Thiruvananthapuram",
        "Madhya Pradesh": "Bhopal",
        Maharashtra: "Mumbai",
        Manipur: "Imphal",
        Meghalaya: "Shillong",
        Mizoram: "Aizawl",
        Nagaland: "Kohima",
        Odisha: "Bhubaneswar",
        Punjab: "Chandigarh",
        Rajasthan: "Jaipur",
        Sikkim: "Gangtok",
        "Tamil Nadu": "Chennai",
        Telangana: "Hyderabad",
        Tripura: "Agartala",
        "Uttar Pradesh": "Lucknow",
        Uttarakhand: "Dehradun",
        "West Bengal": "Kolkata",
      };

      return stateCapitals[state] || "Delhi"; // Default to Delhi if state not found
    };

    // Fetch weather data for the input city
    const fetchWeatherData = async () => {
      const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Your OpenWeatherMap API key
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${cityInput.value}&appid=${apiKey}&units=metric`;

      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error("City not found");
        const data = await response.json();

        updateMap(data);
      } catch (error) {
        console.error(error);
        alert("Error fetching weather data: " + error.message);
      }
    };

    // Update the map with the fetched weather data
    const updateMap = (data) => {
      // Clear existing marker if it exists
      if (marker.value) {
        map.value.removeLayer(marker.value);
      }

      // Add marker for the weather location
      marker.value = L.marker([data.coord.lat, data.coord.lon], {
        icon: L.icon({
          iconUrl: "https://openweathermap.org/img/wn/04d@2x.png", // Different icon for the city marker
          iconSize: [25, 25],
          iconAnchor: [12.5, 25],
        }),
      })
        .addTo(map.value)
        .bindPopup(
          `Temperature: ${data.main.temp} °C<br>Condition: ${data.weather[0].description}`
        )
        .openPopup();

      // Set view to the marker location
      map.value.setView([data.coord.lat, data.coord.lon], 10);
    };

    onMounted(() => {
      initializeMap();
      addStateMarkers(); // Add markers for all states
      fetchWeatherDataForAllStates(); // Fetch weather data for all states
    });

    return {
      cityInput,
      fetchWeatherData,
      fetchWeatherDataForAllStates,
      stateMarkers,
      isRequestInProgress,
    };
  },
};
</script>

<style scoped>
.weather-map {
  max-width: 800px;
  margin: 0 auto;
}
</style>
