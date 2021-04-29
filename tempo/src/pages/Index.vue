<template>
  <q-page>

    <div id="search" class="row justify-center bg-cyan-1">
      <q-input v-model="city" outlined bg-color="cyan-1" color="cyan-8" label="Search city"></q-input>
      <q-btn outline @click="search" id="btn" bg-color="cyan-2" color="cyan-8" icon="search" />
    </div>

    <div id="main">
      <div id="info">
        <p id="date">{{info.date}}</p>
        <p class="text-h4">{{data.name}} {{data.sys.country}}</p>
        <div id="temp" class="flex">
          <img :src="`http://openweathermap.org/img/wn/${info.icon}@2x.png`" :alt="`${info.desc}`">
          <p class="text-h3">{{info.temp}}</p>
        </div>
        <p class="text-center text-h6">{{info.desc}}</p>
        <div id="moreInfo">
          <div id="doubleInfo" class="flex justify-between">
            <p>{{info.windSpeed}}</p>
            <p>{{info.pressure}}</p>
          </div>
          <div id="doubleInfo" class="flex justify-between">
            <p>{{info.humidity}}</p>
            <p>{{info.uvi}}</p>
          </div>
          <div id="doubleInfo" class="flex justify-between">
            <p>{{info.dewPoint}}</p>
            <p>{{info.visibility}}</p>
          </div>
        </div>
      </div>
      <div id="charts">
        <GChart
          id="chart"
          type="LineChart"
          :data="chartData"
          :options="chartOptions"
        />
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'

export default {
  name: 'PageIndex',
  data () {
    return {
      city: 'Dourados',
      data: {
        name: null,
        sys: {
          country: null
        }
      },
      info: {
        date: null,
        icon: null,
        desc: null,
        temp: null,
        windSpeed: null,
        pressure: null,
        humidity: null,
        uvi: null,
        dewPoint: null,
        visibility: null
      },
      chartData: [],
      chartOptions: {}
    }
  },
  methods: {
    search: function () {
      axios.get(`http://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=ed18af83d0ee3e2a01fb4d058adc4ee9`)
        .then(res => {
          this.data = res.data

          const name = this.data.name + ','
          this.data.name = name

          this.city = null

          axios.get(`https://openweathermap.org/data/2.5/onecall?lat=${this.data.coord.lat}&lon=${this.data.coord.lon}&units=metric&appid=439d4b804bc8187953eb36d2a8c26a02`)
            .then(res => {
              this.info = res.data

              const date = new Date(this.info.current.dt * 1000).toLocaleString('pt-br').slice(0, 16)
              this.info.date = date

              const icon = this.info.current.weather[0].icon
              this.info.icon = icon

              const desc = this.info.current.weather[0].description
              this.info.desc = desc[0].toUpperCase() + desc.slice(1)

              const temp = Math.round(this.info.current.temp) + '°C'
              this.info.temp = temp

              const windSpeed = `Wind speed: ${this.info.current.wind_speed} m/s`
              this.info.windSpeed = windSpeed

              const pressure = `Pressure: ${this.info.current.pressure} hPa`
              this.info.pressure = pressure

              const humidity = `Humidity: ${this.info.current.humidity}%`
              this.info.humidity = humidity

              const uvi = `UV: ${Math.round(this.info.current.uvi)}`
              this.info.uvi = uvi

              const dewPoint = `Dew point: ${Math.round(this.info.current.dew_point)}°C`
              this.info.dewPoint = dewPoint

              const visibility = `Visibility: ${Number(this.info.current.visibility) / 1000} km`
              this.info.visibility = visibility

              // hourly
              const hour0 = new Date(this.info.hourly[0].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp0 = Number(Math.round(this.info.hourly[0].temp))

              const hour3 = new Date(this.info.hourly[3].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp3 = Number(Math.round(this.info.hourly[3].temp))

              const hour6 = new Date(this.info.hourly[6].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp6 = Number(Math.round(this.info.hourly[6].temp))

              const hour9 = new Date(this.info.hourly[9].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp9 = Number(Math.round(this.info.hourly[9].temp))

              const hour12 = new Date(this.info.hourly[12].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp12 = Number(Math.round(this.info.hourly[12].temp))

              const hour15 = new Date(this.info.hourly[15].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp15 = Number(Math.round(this.info.hourly[15].temp))

              const hour18 = new Date(this.info.hourly[18].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp18 = Number(Math.round(this.info.hourly[18].temp))

              const hour21 = new Date(this.info.hourly[21].dt * 1000).toLocaleString('pt-br').slice(-8).slice(0, 5)
              const temp21 = Number(Math.round(this.info.hourly[21].temp))

              this.chartData = [
                ['Hour', '°C'],
                [hour0, temp0],
                [hour3, temp3],
                [hour6, temp6],
                [hour9, temp9],
                [hour12, temp12],
                [hour15, temp15],
                [hour18, temp18],
                [hour21, temp21]
              ]

              this.chartOptions = {
                curveType: 'function',
                title: 'Temperature'
              }
            })
        })
    }
  }
}
window.onload = function () {
  document.getElementById('btn').click()
}
</script>

<style scoped>
  #search {
    padding: 30px 0;
  }

  #main {
    max-width: 1024px;

    margin: 0 auto;
    padding: 30px 2%;

    display: flex;
    align-items: center;
  }

  #date {
    font-size: 14px;
    margin-bottom: -1px;
    color: rgb(1, 153, 255);
  }

  #info {
    width: 30%;
  }

  #moreInfo {
    margin-top: 25px;
  }

  #temp {
    display: flex;
    align-items: center;
  }

  #charts {
    width: 70%;
  }

  #chart {
    width: 100%;
    height: 400px;
  }

  @media (max-width: 1024px) {
    #main {
      flex-direction: column;
      padding: 30px 0;
    }
  }
</style>
