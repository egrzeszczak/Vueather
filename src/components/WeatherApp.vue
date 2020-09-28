<template>
    <div class="container">
      <div class="search-box">
        <input
          type="text"
          name="search"
          v-model="query"
          @keypress="fetchLocation"
          placeholder="Podaj lokalizację"
          autocomplete="off"
        />
        <button
          @click="clearLocations"
        >
          &#10799;
        </button>
      </div>
      <transition-group name="slide-fade" class="weather-wrapper">
        <WeatherBox
          v-for="location in locations"
          :key="location.id"
          :location="location"
          :weatherCodes="WeatherCodes"
          @removeLocation="removeLocation"
        >
        </WeatherBox>
      </transition-group>
    </div>
</template>

<script>
// Imports
import axios from "axios";
import WeatherBox from "./WeatherBox.vue";
import WeatherCodes from "../static/WeatherCodes.json"

export default {
  name: "Vueather",
  components: {
    WeatherBox,
  },
  data() {
    return {
      api_key: "", // API !!!
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      next_location_id: 0,
      locations: [],
      WeatherCodes,
    };
  },
  methods: {
    async fetchLocation(e) {
      if (e.key == "Enter") {
        const apiResult = await axios.get(
          `${this.url_base}weather?q=${this.query}&units=metric&lang=pl&APPID=${this.api_key}`
        );
        this.addLocation(apiResult.data);
      }
    },
    addLocation(apiResult) {
      this.locations.push({
        id: this.next_location_id,
        name: apiResult.name,
        data: apiResult,
        revealed: false,
      });
      this.next_location_id++;
      this.query = "";
    },
    removeLocation(location_to_remove) {
      const index = this.locations.findIndex((el) => el.id === location_to_remove);
      this.locations.splice(index, 1);
    },
    clearLocations(){
      this.locations = [];
    }
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Baloo+Tammudu+2&display=swap");

$vibe: rgb(27, 72, 114);

body,
html {
  padding: 0;
  margin: 0;
  background-image: url("https://images.unsplash.com/photo-1556706995-b5b014972b9c?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1323&q=80");
  background-color: $vibe;
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: center;
  color: white;
  font-family: "Baloo Tammudu 2";

  .slide-fade-enter-active {
    transition: all 0.3s ease;
  }
  .slide-fade-leave-active {
    transition: all 0.5s cubic-bezier(1, 0.5, 0.8, 1);
    width: 0px;
  }
  .slide-fade-enter, .slide-fade-leave-to
    /* .slide-fade-leave-active below version 2.1.8 */ {
    transform: translateX(10px);
    opacity: 0;
  }

  .container {
    border: 1px solid transparent;
    margin: 2rem;
    height: calc(100vh - 4rem - 2px);
    .search-box {
      display: flex;
      justify-content: center;
      margin-top: 1rem;
      input {
        background: $vibe;
        border: 0;
        padding: 0.5rem 1rem;
        margin: 0 .5rem;
        color: white;
        width: 300px;
        text-align: center;
        transition: 0.25s all cubic-bezier(0.075, 0.82, 0.165, 1);
        transition-delay: .15s;
        &:hover {
          background: white;
          color: black;
        }
        &:focus {
          width: 450px;
          background: white;
          color: black;
        }
      }
      button{
        height: 34px;
        width: 34px;
        border: 1px solid transparent;
        background: none;
        color: white;
        transition: .25s all cubic-bezier(0.39, 0.575, 0.565, 1);
        &:hover{
          border: 1px solid white;
          background: rgb(238, 87, 87);
          color: white;
          cursor: pointer;
        }
      }
    }
    .weather-wrapper {
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
