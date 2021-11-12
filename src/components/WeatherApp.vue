<template>
 <div>
     <input v-model="city" type="text" placeholder="City"/>
     <button @click="searchLocation">Search</button>
     <div v-if="currentConditions">
         {{ temperature}} - {{ text }}
        <img :src="getWeatherIcon" />
     </div>
 </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'WeatherApp',
  data() {
      return {
          city:'',
          locationKey: '',
        currentConditions: ''
      }
  },
  computed: {
      text() {
          return this.currentConditions[0].WeatherText
      },
      temperature(){
          return `${this.currentConditions[0].Temperature.Metric.Value} ºC`
      },
      icon() {
          return this.currentConditions[0].WeatherIcon.toString().padStart(2, '0')
      },
      getWeatherIcon() {
          return `https://developer.accuweather.com/sites/default/files/${this.icon}-s.png`
      }
  },
  methods: {
      async searchLocation() {
          try {
              const { data } = await axios.get(`http://dataservice.accuweather.com/locations/v1/cities/search?apikey=5GGYUeBx9tX9NgvDRoVqYzRor87N364q&q=`+this.city)
              this.locationKey = data[0].Key 
              this.getCurrentConditions()
          } catch(e) {
              alert(`Erro: ` + e)
          }
      },
      async getCurrentConditions() {
          try {
              const { data } = await axios.get(`http://dataservice.accuweather.com/currentconditions/v1/${this.locationKey}?apikey=5GGYUeBx9tX9NgvDRoVqYzRor87N364q&language=pt-br`)
              this.currentConditions = data
          } catch(e){
              alert(`Erro: ` + e)
          }
          

      }
  },
  created() {
    //   if (this.locationKey && !this.currentConditions) {
    //       this.getCurrentConditions()
    //   }
  }
}
</script>

<style scoped>
</style>
