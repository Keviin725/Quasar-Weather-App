<template>
  <q-page class="flex column">

    <div class="col q-pa-md">

      <q-input v-model="search" placeholder="Search" dark borderless @keyup.enter="getWeatherBySearch">

        <template v-slot:before>
          <q-icon name="my_location" @click="getLocation" />
        </template>


        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getWeatherBySearch"/>
        </template>

      </q-input>

    </div>

    <template v-if="weatherData">

      <div class="col text-white text-center">

        <div class="text-h4 text-weight-light">
          {{ weatherData.name }}
        </div>

        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div>

        <div class="text-h1 text-weight-thin q-my-lg">
          {{ Math.round(weatherData.main.temp) }}&deg;C
        </div>

      </div>

      <div class="col text-center">
        <img :src="'http://openweathermap.org/img/wn/${this.weatherData.icon}2x.png'">
      </div>

    </template>

    <template v-else>

      <div class="col column text-start text-white">
        <div class="col text-h2 text-weight-thin q-pa-sm">
          Weather <br> APP
        </div>
        <q-btn class="col" flat @click="getLocation">
          <q-icon left size="2em" name="my_location" />
          <div>Find My Location</div>
        </q-btn>
      </div>

    </template>

    <div class="col skyline">
    </div>

  </q-page>
</template>

<script>





export default ({
  name: 'IndexPage',
  data() {
    return {

      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: 'PUT YOUR OWN API KEY'
    }
  },
  methods: {
    getLocation() {
      navigator.geolocation.getCurrentPosition(position => {
        console.log('position: ', position)
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getWeatherByCoords()
      })
    },
   async getWeatherByCoords() {
      await this.$axios('${this.apiUrl}?${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric')
        .then(response => {
          this.weatherData = response.data
          console.log('WeatherData', this.weatherData)

        })
        .catch(error =>{
          console.log(error)
        })
    },
    getWeatherBySearch(){
      this.$axios('${this.apiUrl}?q=${this.search}&appid=${ this.apiKey }')
        .then(response => {
          this.weatherData = response.data
          console.log(this.weatherData)

        })
        .catch(error =>{
          console.log(error)
        })
    }
  }
})
</script>
<style lang="scss">
.q-page {
  background: linear-gradient(to bottom, #136a8a, #267871)
}

.skyline {
  flex: 0 0 150px;
  background-image: url(town.png);
  background-size: contain;
  background-position: bottom;
  height: auto;
  padding-bottom: 0;
}
</style>
