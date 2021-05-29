<template>
  <div id="app" :class="isDay ? 'day' : 'night'">
    <div class="container my-5">
      <h1 class="title text-center">Weather in</h1>
      <form class="search-location" v-on:submit.prevent="getWeather">
        <input
          type="text"
          class="form-control text-muted form-rounded p-4 shadow-sm"
          placeholder="What City?"
          v-model="citySearch"
          autocomplete="off"
        />
      </form>
      <p class="text-center my-3" v-if="cityFound">No city found</p>
      <div
        class="card rounded my-3 shadow-lg back-card overflow-hidden"
              v-if="visible">
        <div class="card-top text-center" style="margin-bottom: 15rem">
          <div class="city-name my-3">
            <p>{{ weather.cityName }}</p>
            <span>...</span>
            <p class="">{{ weather.country }}</p>
          </div>
        </div>
        <!--card body used cos I want to just update the innerHTML -->
        <div class="card-body">
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span>{{ weather.temperature }}&deg;C</span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col d-flex justify-content-between px-5 mx-5">
                <p>
                  <img />
                  {{ weather.lowTemp }}&deg;C
                </p>
                <p>
                  <img/>
                  {{ weather.highTemp }}&deg;C
                </p>
              </div>
            </div>
          </div>
          <div class="card-bottom px-5 py-4 row">
            <div class="col text-center">
              <p>{{ weather.feelsLike }}&deg;C</p>
              <span>Feels like</span>
            </div>
            <div class="col text-center">
              <p>{{ weather.humidity }}%</p>
              <span>humidity</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      cityFound: false,
      visible: false,
      citySearch: "",
      isDay : true,
      weather: {
        cityName: "Bishkek",
        country: "Kyrgyzstan",
        temperature: 34,
        description: "Sunny",
        lowTemp: "17",
        highTemp: "34",
        feelsLike: "40",
        humidity: "55",
      }
    }
  },
  
  methods: {
    getWeather: async function () {
      const key = "5299e83c9cf2fef510fb8ddaa3206ac8";
      const baseURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`
      try {
        const response = await fetch(baseURL)
        const data = await response.json()
        console.log(data)
        this.citySearch = "";
        this.weather.cityName = data.name
        this.weather.country = data.sys.country
        this.weather.temperature = Math.round(data.main.temp)
        this.weather.description = data.weather[0].description
        this.weather.lowTemp = Math.round(data.main.temp_min)
        this.weather.highTemp = Math.round(data.main.temp_max)
        this.weather.feelsLike = Math.round(data.main.feels_like)
        this.weather.humidity = Math.round(data.main.humidity)
        const timeOfDay = data.weather[0].icon
        //check for time of day
        if(timeOfDay.includes("n")){
            this.isDay = false
        }
        else {
            this.isDay = true;
        }
      
         this.cityFound = false;
         this.visible = true;

      } catch(error) {
        console.log(error);
        this.cityFound = true;
        this.visible = false;
      }
    }
  }
}
</script>

<style>
    @import './assets/custom.css';
</style>
