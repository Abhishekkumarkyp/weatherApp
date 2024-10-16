<template>
    <div class="container my-4">
      <h1 class="text-center">Air Pollution Concept</h1>
      <div class="card">
        <div class="card-body">
          <h5 class="card-title">Air Quality Information</h5>
          <p class="card-text">{{ apiDescription }}</p>
          <p class="card-text">Pollutants:</p>
          <ul class="list-group mb-3">
            <li class="list-group-item" v-for="pollutant in pollutants" :key="pollutant">
              {{ pollutant }}
            </li>
          </ul>
          <p class="card-text">{{ forecastDescription }}</p>
          <h5 class="mt-4"> AQI Scale</h5>
          <table class="table table-striped">
            <thead>
              <tr>
                <th scope="col">Qualitative Name</th>
                <th scope="col">Index</th>
                <th scope="col">Pollutant Concentration (μg/m³)</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="level in aqiScale" :key="level.index">
                <td>{{ level.name }}</td>
                <td>{{ level.index }}</td>
                <td>{{ level.concentration }}</td>
              </tr>
            </tbody>
          </table>
          <p class="card-text">{{ otherParameters }}</p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        apiDescription: 'The Air Pollution API provides current, forecast, and historical air pollution data for any coordinates on the globe.',
        pollutants: [
          'Carbon monoxide (CO)',
          'Nitrogen monoxide (NO)',
          'Nitrogen dioxide (NO2)',
          'Ozone (O3)',
          'Sulphur dioxide (SO2)',
          'Ammonia (NH3)',
          'Particulates (PM2.5 and PM10)',
        ],
        forecastDescription: 'Air pollution forecast is available for 4 days with hourly granularity. Historical data is accessible from 27th November 2020.',
        aqiScale: [
          { name: 'Good', index: 1, concentration: 'SO2: [0; 20), NO2: [0; 40), PM10: [0; 20), PM2.5: [0; 10), O3: [0; 60), CO: [0; 4400)' },
          { name: 'Fair', index: 2, concentration: 'SO2: [20; 80), NO2: [40; 70), PM10: [20; 50), PM2.5: [10; 25), O3: [60; 100), CO: [4400; 9400)' },
          { name: 'Moderate', index: 3, concentration: 'SO2: [80; 250), NO2: [70; 150), PM10: [50; 100), PM2.5: [25; 50), O3: [100; 140), CO: [9400; 12400)' },
          { name: 'Poor', index: 4, concentration: 'SO2: [250; 350), NO2: [150; 200), PM10: [100; 200), PM2.5: [50; 75), O3: [140; 180), CO: [12400; 15400)' },
          { name: 'Very Poor', index: 5, concentration: 'SO2: ⩾350, NO2: ⩾200, PM10: ⩾200, PM2.5: ⩾75, O3: ⩾180, CO: ⩾15400' },
        ],
        otherParameters: 'Other parameters that do not affect the AQI calculation: NH3: min value 0.1 - max value 200, NO: min value 0.1 - max value 100.',
      };
    },
  };
  </script>
  
  <style scoped>
  .container {
    max-width: 800px; /* Limit the width of the card */
    margin: auto; /* Center the card */
  }
  
  .card {
    border-radius: 15px; /* Rounded corners */
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1); /* Subtle shadow */
  }
  
  .card-title {
    font-size: 1.75rem; /* Larger font size for title */
  }
  
  .table th, .table td {
    vertical-align: middle; /* Center-align table content */
  }
  </style>
  