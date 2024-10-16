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
import L from "leaflet";

export default {
  data() {
    return {
      cityInput: "Delhi", // Default city
      map: null,
      marker: null,
      stateMarkers: [],
      stateCoordinates: {
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
      },
    };
  },
  mounted() {
    this.initializeMap();
    this.addStateMarkers(); // Add markers for all states
    this.fetchWeatherData(); // Fetch initial weather data
  },
  methods: {
    initializeMap() {
      this.map = L.map("map").setView([20.5937, 78.9629], 5); // Center the map on India

      // Add OpenStreetMap tiles
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        maxZoom: 19,
      }).addTo(this.map);
    },

    addStateMarkers() {
      Object.entries(this.stateCoordinates).forEach(([state, coords]) => {
        const marker = L.marker(coords, { icon: this.getStateMarkerIcon() })
          .addTo(this.map)
          .bindPopup(`${state}`);

        this.stateMarkers.push(marker);
      });
    },

    getStateMarkerIcon() {
      return L.divIcon({
        className: "state-marker",
        html: '<div style="background-color: blue; border-radius: 50%; width: 12px; height: 12px;"></div>',
        iconSize: [12, 12],
      });
    },

    async fetchWeatherData() {
      const apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY; // Your OpenWeatherMap API key
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.cityInput}&appid=${apiKey}&units=metric`;

      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error("City not found");
        const data = await response.json();

        this.updateMap(data);
      } catch (error) {
        console.error(error);
        alert("Error fetching weather data: " + error.message);
      }
    },

    updateMap(data) {
      // Clear existing marker
      if (this.marker) {
        this.map.removeLayer(this.marker);
      }

      // Add marker for the weather location
      this.marker = L.marker([data.coord.lat, data.coord.lon], {
        icon: this.getCityMarkerIcon(),
      })
        .addTo(this.map)
        .bindPopup(
          `Temperature: ${data.main.temp} °C<br>Condition: ${data.weather[0].description}`
        )
        .openPopup();

      // Set view to the marker location
      this.map.setView([data.coord.lat, data.coord.lon], 10);
    },

    getCityMarkerIcon() {
      return L.divIcon({
        className: "city-marker",
        html: '<div style="background-color: red; border-radius: 50%; width: 12px; height: 12px;"></div>',
        iconSize: [12, 12],
      });
    },
  },
};
</script>

<style scoped>
.weather-map {
  max-width: 800px;
  margin: 0 auto;
}
.state-marker {
  background-color: blue; /* You can also use an icon instead of a circle */
  border-radius: 50%;
  width: 12px;
  height: 12px;
}

.city-marker {
  background-color: red; /* Different color for the city marker */
  border-radius: 50%;
  width: 12px;
  height: 12px;
}
</style>
