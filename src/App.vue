<template>
  <div id="app">
    <div class="container">
      <div class="search-box">
        <input 
          type="text" 
          name="search" 
          id="" 
          v-model="query"
          @keypress="getWeather"
          placeholder="Nazwa miasta"
        >
      </div>
      <!--  -->
      <transition-group name="slide-fade" class="weather-wrapper">
        <WeatherBox
          v-for="place in places"
          :key="place.id"
          :place="place"
          @removeWeather="removeWeather"
          >
        </WeatherBox>
      </transition-group>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import WeatherBox from "./components/WeatherBox.vue";

export default {
  components: {
    WeatherBox: WeatherBox,
  },
  name: 'App',
  data() {
    return {
      api_key: '',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      nextId: 0,
      places: [],
    }
  },
  methods: {
    async getWeather(e) {
      if (e.key == "Enter"){
        const res = await axios.get(`${this.url_base}weather?q=${this.query}&units=metric&lang=pl&APPID=${this.api_key}`)
        this.addWeather(res.data)
      }
    },
    addWeather(res){
      this.places.push(
        {id: this.nextId, name: res.name, data: res, expandDetails: false}
      )
      this.nextId++
      this.query = ""
    },
    removeWeather(removeId){
      // const index = this.items.findIndex(el => el.id === taskId); 
      // this.items[index].completed = true;
      const index = this.places.findIndex(el => el.id === removeId);
      this.places.splice(index, 1)
    },
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Baloo+Tammudu+2&display=swap');

  body, html{
    padding: 0;
    margin: 0;
    background-image: url('https://images.unsplash.com/photo-1556706995-b5b014972b9c?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1323&q=80');
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-position: center; 
    color: white;
    font-family: 'Baloo Tammudu 2';

    .slide-fade-enter-active {
      transition: all .3s ease;
    }
    .slide-fade-leave-active {
      transition: all .5s cubic-bezier(1.0, 0.5, 0.8, 1.0);
      width: 0px;
    }
    .slide-fade-enter, .slide-fade-leave-to
    /* .slide-fade-leave-active below version 2.1.8 */ {
      transform: translateX(10px);
      opacity: 0;
    }

    .container{
      border: 1px solid transparent;
      margin: 2rem;
      height: calc(100vh - 4rem - 2px);
      .search-box{
        display: flex;
        justify-content: center;
        input{
          margin-top: 1rem;
          background: rgb(27, 72, 114);
          border: 0;
          padding: .5rem 1rem;
          color: white;
          width: 300px;
          text-align: center;
          transition: .25s all cubic-bezier(0.075, 0.82, 0.165, 1);
          &:hover{
            background: white;
            color: black;
          }
          &:focus{
            width: 450px;
            background: white;
            color: black;
          }
        }
      }
      .weather-wrapper{
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 1rem;
        overflow-x: visible;
        flex-wrap: nowrap;
      }
    }
  }

</style>
