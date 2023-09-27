<template>
  <el-row>
    <el-card class="card">
      <p class="getWeatherDetailsText">WEATHER DETAILS</p>
      <el-input v-model="base.cityName" clearable placeholder="Enter city name (ex) chennai" class="cardInput"
        style="border-radius: 10px;"></el-input>
      <el-button @click="getWeatherDetails" class="searchButton"><el-icon>
          <Search />
        </el-icon></el-button>
      <el-row>
        <img :src='base.png' class="pngImageSize" alt="">
      </el-row>
      <h1 v-if="base.showDetailes && base.getWeatherDetailes.cod == 200">{{
        `${Math.round(base.getWeatherDetailes.main.temp)}°C` }}</h1>
      <h1 v-if="base.showDetailes && base.getWeatherDetailes.cod == 200">{{ `${base.getWeatherDetailes.name},
              ${base.getWeatherDetailes.sys.country}` }}</h1>
      <el-row style="display: flex; justify-content: space-around;">
        <img v-if="base.getWeatherDetailes.main && base.getWeatherDetailes.cod == 200" src="../assets/humidity.png"
          class="humidityPNG">
        <img v-if="base.getWeatherDetailes.wind && base.getWeatherDetailes.cod == 200" src="../assets/wind.png"
          class="windPng">
      </el-row>
      <el-row style="display: flex; justify-content: space-around;">
        <h3 v-if="base.getWeatherDetailes.main && base.getWeatherDetailes.cod == 200">{{ `Humidity:
          ${base.getWeatherDetailes.main.humidity} %` }}</h3>
        <h4 v-if="base.getWeatherDetailes.wind && base.getWeatherDetailes.cod == 200">{{ `Wind:
          ${base.getWeatherDetailes.wind.speed} km/h` }}</h4>
      </el-row>
      <h1 v-if="base.showNotFound" style="color:coral">{{ base.getWeatherDetailes.message }}</h1>

    </el-card>
  </el-row>
</template>

<script>
import { reactive, onMounted } from 'vue';
import cloudyPNG from '../assets/cloudy.png'
import rainPNG from '../assets/rain.png'
import sunPNG from '../assets/sun.png'
import { ElNotification } from 'element-plus'
import apiKey from '../key.js'

export default {
  name: "weatherApp",

  setup() {
    const base = reactive({
      cityName: '',
      getWeatherDetailes: {},
      showDetailes: false,
      showNotFound: false,
      png: cloudyPNG
    })
    const pngs = reactive([{ icon: 'Rain', png: rainPNG }, { icon: 'Haze', png: sunPNG }, { icon: 'Clouds', png: cloudyPNG }])
    onMounted(() => {
      base.cityName = 'chennai',
      getWeatherDetails(base.cityName)
      console.log('mounted')
    })

    const getWeatherDetails = async (cityName) => {
      const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?units=metric&q=${base.cityName == '' ? cityName : base.cityName}&appid=${apiKey.apiKeyG}`)
      base.getWeatherDetailes = await response.json()
      if (base.getWeatherDetailes.cod == 404) {
        ElNotification({
          title: 'Error',
          message: base.getWeatherDetailes.message,
          type: 'error',
          position: 'top-right',
        })
        base.showNotFound = true
      }
      base.showDetailes = true
      pngs.forEach((item) => {
        if (base.getWeatherDetailes.weather[0].main == item.icon) {
          base.png = item.png
        }

      })

      console.log(base.getWeatherDetailes.weather[0])

    }

    return {
      base,
      getWeatherDetails
    }
  },
};
</script>

<style>
@media (max-width:800px) and (min-width: 100px) {
  .card {
    height: 470pt;
    width: 296pt;
    margin-top: 1em;
    margin-left: 1px;
    margin-right: 1px;
    background-image: linear-gradient(150deg, #e964c1, #6f39ad);
  }

  .getWeatherDetailsText {
    margin-top: 50pt;
    font-size: xx-large;
    font-weight: 900;
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  }

  .card .cardInput {
    width:232px;
    margin-left: 30px;
  }

  .card .pngImageSize {
    width: 120px;
    height: 120px;
    margin-left: 7em;
    margin-top: 32px;
  }
}
</style>
