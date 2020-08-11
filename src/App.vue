<template>
  <div id="app">
    <div class="container">
      <h3 class="font-weight-bold pb-3"><u>Weather Checker</u></h3>
          <form @submit="submit">
            <div class="form-group">
              <input class="form-control d-inline m-auto w-25" placeholder="Enter city name" id="weatherInput" v-model="weatherInput" type="text" />
              <button class="btn btn-primary ml-1" type="submit">Search</button>
            </div>
          </form>
          <div class="border rounded w-50 m-auto pb-3 bg-white" v-if="weatherIcon || weatherBadInput || weatherError">
            <img class="rounded m-2 small-shadow" v-if="weatherIcon" :src="weatherIcon" />
            <div v-if="weatherDescription">
              The Current Weather in {{ weatherCity }} is: {{ weatherDescription }}
            </div>
            <div v-if="weatherTemp">With a temperature of {{ weatherTemp }}℃,</div>
            <div v-if="weatherWind">windspeed of {{ weatherWind	 }}m/s and humidity of {{ weatherHumidity }}%</div>
            <div v-if="weatherError">{{ weatherError }}</div>
            <div v-if="weatherBadInput">{{ weatherBadInput }}</div>
          </div>
    </div>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    data() {
      return {
        weatherInput: "",
        weatherIcon: "",
        weatherIconToken: "",
        weatherError: "",
        weatherBadInput: "",
        weatherDescription: "",
        weatherTemp: "",
        weatherWind: "",
        weatherHumidity: "",
        loading: false
      };
    },
    methods: {
      submit(event) {
        event.preventDefault();
        if (this.weatherInput === "") {
          this.weatherError = "Please enter a city";
          this.weatherDescription = "";
          this.weatherBadInput = "";
          this.weatherDescription = "";
          this.weatherIconToken = "";
          this.weatherIcon = "";
          this.weatherTemp = "";
          this.weatherWind = "";
          this.weatherHumidity = "";
          return;
        }
        if (this.loading) {
          return;
        }
        var weatherCity = this.weatherInput;
        this.weatherBadInput = "";
        this.weatherError = "";
        this.weatherDescription = "";
        this.weatherIconToken = "";
        this.weatherIcon = "";
        this.weatherTemp = "";
        this.weatherWind = "";
        this.weatherHumidity = "";
        this.loading = true;
        axios
          .get(
            "https://api.openweathermap.org/data/2.5/weather?q=" +
            weatherCity +
            "&APPID=6b5310389b0e38fe0434476c2015561e&units=metric"
          )
          .then(response => {
            console.log(response);
            this.weatherIconToken = response.data.weather[0].icon;
            this.weatherIcon =
              "http://openweathermap.org/img/w/" + this.weatherIconToken + ".png";
            this.weatherTemp = response.data.main.temp;
            this.weatherDescription = response.data.weather[0].description;
            this.weatherDescription =
              this.weatherDescription.charAt(0).toUpperCase() +
              this.weatherDescription.slice(1);
            this.weatherCity = response.data.name;
            this.weatherWind = response.data.wind.speed;
            this.weatherHumidity = response.data.main.humidity;
            this.loading = false;
          })
          .catch(error => {
            console.error(error);
            this.weatherBadInput = "Please enter valid city-name.";
            this.weatherCity = weatherCity;
            this.loading = false;
          });
      }
    }
  };
</script>

<style>
  body {
    background-color: #f8f9fa !important;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  @import'~bootstrap/dist/css/bootstrap.css'
</style>