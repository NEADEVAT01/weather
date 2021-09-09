<template>
  <div v-bind:class="{daytime: isDay, nighttime: !isDay}" class="main row align-items-center m-0">
    <div v-bind:class="{'bg-light text-black': isDay, 'bg-dark text-white': !isDay}"
      class="container w-75  mh-100 shadow rounded px-3 py-2">
      <p class="display-2">{{info.name}}</p>
      <p class="display-5 ">Now {{date}}.{{month}}.{{year}} {{hours}}:{{minutes}}:{{seconds}}</p>
      <div class="row">
        <div class="col-lg-3 col-md-6 col-sm-6 text-center p-0 ">
          <p class="display-3 ">{{info.main.temp}} C°</p>
        </div>
        <div class="col-lg-2 col-md-6 col-sm-6
        p-0 text-center"><img :src="icon_url" alt="Weather Icon"></div>
        <div class="col-lg-7 col-md-12 col-sm-12">
          <p class="display-3 m-0">{{info.weather[0].description}}</p>
        </div>

      </div>
      <div class="row">
        <div class="col-lg-3 col-md-6 col-sm-12 text-center">
          <p class="display-5 text-break m-0">Feels like</p>
          <p class="lead"> <br> {{info.main.feels_like}} C°</p>
        </div>
        <div class="col-lg-3 col-md-6 col-sm-12 text-center">
          <img v-if="isDay" src="../assets/icon-wind-day.png" alt="wind-icon" class="img-fluid">
          <img v-if="!isDay" src="../assets/icon-wind-night.png" alt="wind-icon" class="img-fluid">
          <p class="lead">{{info.wind.speed}} m/s</p>
        </div>
        <div class="col-lg-3 col-md-6 col-sm-12 text-center">
          <img v-if="isDay" src="../assets/icon-humidity-day.png" alt="humidity-icon" class="img-fluid">
          <img v-if="!isDay" src="../assets/icon-humidity-night.png" alt="humidity-icon" class="img-fluid">
          <p class="lead">{{info.main.humidity}}%</p>
        </div>
        <div class="col-lg-3 col-md-6 col-sm-12 text-center">
          <img v-if="isDay" src="../assets/icon-pressure-day.png" alt="pressure-icon" class="img-fluid">
          <img v-if="!isDay" src="../assets/icon-pressure-night.png" alt="pressure-icon" class="img-fluid">
          <p class="lead">Pressure: {{info.main.pressure}} mb</p>
        </div>
        <h4 class="text-center">Fact of the Day</h4>
        <div class="row">
          <div class="col-3 text-center d-flex align-items-center justify-content-center"><img :src='info_nasa.url' alt="nasa-img" class="img-fluid w-75"></div>
          <div class="col">
          <p class="lead"><strong>{{info_nasa.title}}</strong></p>
            <p class="lead">{{info_nasa.explanation}}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
  import axios from 'axios'

  export default {
    name: 'home',
    data() {
      return {
        info: {},
        info_nasa: {},
        icon_url: '',
        isDay: true,
        date: 0,
        month: 0,
        year: 0,
        hours: 0,
        minutes: 0,
        seconds: 0
      }
    },
    methods: {
      setTime() {
        setInterval(() => {
          const date = new Date()
          this.date = date.getDate()
          this.month = this.checkSingleDigit(date.getMonth())
          this.year = date.getFullYear()
          this.hours = date.getHours()
          this.minutes = this.checkSingleDigit(date.getMinutes())
          this.seconds = this.checkSingleDigit(date.getSeconds())
        }, 1000)
      },
      checkSingleDigit(digit) {
        return ('0' + digit).slice(-2)
      }
    },
    created: function () {
      axios.
      get(
          "http://api.openweathermap.org/data/2.5/weather?q=634000&appid=69a5b432bff153a6b728793210faa15a&&units=metric"
        )
        .then((response) => (this.info = response.data,
          this.icon_url = "http://openweathermap.org/img/wn/" + response.data.weather[0].icon + "@2x.png",
          this.isDay = response.data.dt > response.data.sys.sunrise)).then();
      axios.
      get(
          "https://api.nasa.gov/planetary/apod?api_key=dFRb9PdqoucjHz4xRoVS3m32ih2jCjmdzCWq0sdq")
        .then((response) => (this.info_nasa = response.data));
      this.setTime()
    }
  }
</script>


<style>
  .main {
    height: 100%;
  }

  .daytime {
    background: url("../assets/day-bg.png");
  }

  .nighttime {
    background: url("../assets/night-bg.png");
  }

  .shadow {
    box-shadow: 0px 0px 22px 14px rgba(0, 0, 0, 0.2) !important;
  }
</style>