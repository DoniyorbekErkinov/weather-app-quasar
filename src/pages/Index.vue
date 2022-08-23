<template>
  <!-- eslint-disable -->
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input @keyup.enter="getWeatherByCityName" borderless dark v-model="search" placeholder="Search">
        <template v-slot:before>
          <q-icon @click="getLocation" name="my_location" />
        </template>
        <template v-slot:append>
          <q-btn @click="getWeatherByCityName" round dense flat icon="search" />
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
        <div class="text-h2 text-weight-thin text-center q-my-lg relative-position">
          <span>{{ weatherData.main.temp }}</span>
          <span class="text-h4 relative-position degree">&deg;</span>
        </div>
      </div>

      <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" />
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Quasar<br>Weather
        </div>
        <q-btn class="col" flat>
          <q-icon @click="getLocation" left size="3em" name="my_location" />
          <div>Find My Location</div>
        </q-btn>
      </div>
    </template>

    <div class="col istanbul">

    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      apiKey: 'df4e82bdb29681c5f17c8b508d734fa8',
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather'
    }
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith('n')) {
          return 'bg-night'
        } else {
          return 'bg-day'
        }
      } else {
        return 'not-found'
      }
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show()

      if (this.$q.platform.is.electron) {
        this.$axios(`https://api.ipbase.com/v2/info?apikey=ktDYODGzeWZ7g0jPSadkigJlP0TVOyfpafWsaVM7`).then(res => {
          console.log(res)
          this.lat = res.data.data.location.latitude
          this.lon = res.data.data.location.longitude
          this.getWeatherByCoords()
        })
      } else {
        navigator.geolocation.getCurrentPosition(position => {
          this.lat = position.coords.latitude
          this.lon = position.coords.longitude
          this.getWeatherByCoords()
        })
      }

    },
    getWeatherByCoords() {
      this.$q.loading.show()
      this.$axios(`${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric
`).then(res => {
        console.log(res)
        this.weatherData = res.data
        this.$q.loading.hide()
      })
    },
    getWeatherByCityName() {
      this.$q.loading.show()
      this.$axios(`${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric
`).then(res => {
        console.log(res)
        this.weatherData = res.data
        this.$q.loading.hide()
      })
    }
  },
  created() {

  }
}
</script>
<style lang="sass">
.degree 
  top: -50px
.istanbul
  flex: 0 0 100px
  background: url(../assets/istanbul.png)
  background-size: contain
  background-position: center bottom
.bg-night 
  background: linear-gradient(to right, #004e92, #000428)
.bg-day 
  background: linear-gradient(to right, #50c9c3, #96deda)
.not-found 
  background: linear-gradient(to right, #26D0CE, #1A2980) 
</style>
