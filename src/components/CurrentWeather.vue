<template>
  <div  class="current-weather">
    <div class="container">
        <div class="weather-info">
            <span class="city">{{this.currentWeather.name }}></span>
            <span class="current-temperature">{{Math.round(this.currentWeather,main.temp)}}</span>
        </div>
        <div class="high-low-temperatures">
            <div class="high">
                <i class="fas fa-chevron-up"></i>
                <span>{{ Math.round(this.currentWeather.main.temp_max) }}&deg;</span>
            </div>
            <div class="low">
                <i class="fas fa-chevron-down"></i>
                <span>{{ Math.round(this.currentWeather.main.temp_min) }}&deg;</span>
            </div>
        </div>
        <span class="condition">{{ this.currentWeather.weather[0].description }}</span>
        <span class="feels-like">Feels like {{ Math.round(this.currentWeather.main.feels_like) }}&deg;</span>
        <div class="weather-icon">
        <img v-if="isDay" src="../assets/moon.png" alt="" />
        <img v-if="isNight" src="../assets/sun.png" alt="" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
    name:"currentWeather",
    props: ["isDay", "isNight", "currentWeather"],
};
</script>

<style lang="scss" scoped>

</style>>

