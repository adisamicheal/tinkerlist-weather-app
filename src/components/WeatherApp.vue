<template>
  <div>
    <div class="weather">
      <div class="weather__container">
        <header>
          <div class="weather__container__location">
            <img src="../../src/assets/location.svg" alt="location icon">
            <p>{{ location || 'Location'}} {{ weatherData ? '-' : '' }} {{ weatherData?.timezone || '' }}</p>
          </div>
        </header>
        <vue-google-autocomplete
          ref="address"
          id="map"
          classname="form-control"
          placeholder="Start typing" 
          v-on:placechanged="getAddressData"
        >
        </vue-google-autocomplete>
        <section class="weather__container__body">
          <div v-if="isLoading">
            <LoadingComponent />
          </div>
          <div v-else class="weather__container__body__left">
            <p>
              {{ 
                weatherData ? weatherData?.current.weather[0].main : ''
              }} 
                - 
              {{ 
                weatherData ? weatherData?.current.weather[0].description : ''
              }}
            </p>
            <h1>
              {{ 
                weatherData ? (weatherData?.current.temp - 273.15).toFixed() : ''
              }}ºC
            </h1>
            <p>Today's High - 
              {{ 
                weatherData ? (weatherData?.daily[0].temp.max - 273.15).toFixed() : ''
              }}ºC
            </p>
            <p>Today's Low -
              {{ 
                weatherData ? (weatherData?.daily[0].temp.min - 273.15).toFixed() : ''
              }}ºC
            </p>
              <!-- -->
            <button
              v-if="weatherData"
              @click="isOpen = true">
                More Details
            </button>
          </div>
          <div class="weather__container__body__right">
            <img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCA0OCA0OCIgd2lkdGg9Ijk2cHgiIGhlaWdodD0iOTZweCI+PHJhZGlhbEdyYWRpZW50IGlkPSJjVUZSVVFXY2w4RnpPNmVhajBrRXhhIiBjeD0iMTkuOTA2IiBjeT0iMjQuODc2IiByPSIxOS4wMDkiIGdyYWRpZW50VW5pdHM9InVzZXJTcGFjZU9uVXNlIj48c3RvcCBvZmZzZXQ9Ii4zMTMiIHN0b3AtY29sb3I9IiNmY2ZjZmMiLz48c3RvcCBvZmZzZXQ9Ii40NjUiIHN0b3AtY29sb3I9IiNlZmYwZjEiLz48c3RvcCBvZmZzZXQ9Ii43NDEiIHN0b3AtY29sb3I9IiNjZWQxZDMiLz48c3RvcCBvZmZzZXQ9IjEiIHN0b3AtY29sb3I9IiNhYWFmYjMiLz48L3JhZGlhbEdyYWRpZW50PjxwYXRoIGZpbGw9InVybCgjY1VGUlVRV2NsOEZ6TzZlYWowa0V4YSkiIGQ9Ik0xNiw3QzkuOTI1LDcsNSwxMS45MjUsNSwxOHM0LjkyNSwxMSwxMSwxMXMxMS00LjkyNSwxMS0xMVMyMi4wNzUsNywxNiw3eiIvPjxyYWRpYWxHcmFkaWVudCBpZD0iY1VGUlVRV2NsOEZ6TzZlYWowa0V4YiIgY3g9IjE1LjcxIiBjeT0iMTMuMjUiIHI9IjMuNDU2IiBncmFkaWVudFVuaXRzPSJ1c2VyU3BhY2VPblVzZSI+PHN0b3Agb2Zmc2V0PSIuMzEzIiBzdG9wLWNvbG9yPSIjOWRhMmE2IiBzdG9wLW9wYWNpdHk9IjAiLz48c3RvcCBvZmZzZXQ9IjEiIHN0b3AtY29sb3I9IiM5ZGEyYTYiLz48L3JhZGlhbEdyYWRpZW50PjxwYXRoIGZpbGw9InVybCgjY1VGUlVRV2NsOEZ6TzZlYWowa0V4YikiIGQ9Ik0xNSwxMGMtMS4xMDUsMC0yLDAuODk1LTIsMnMwLjg5NSwyLDIsMnMyLTAuODk1LDItMlMxNi4xMDUsMTAsMTUsMTB6Ii8+PHJhZGlhbEdyYWRpZW50IGlkPSJjVUZSVVFXY2w4RnpPNmVhajBrRXhjIiBjeD0iMTAuMzU1IiBjeT0iMTguNjI1IiByPSIxLjcyOCIgZ3JhZGllbnRVbml0cz0idXNlclNwYWNlT25Vc2UiPjxzdG9wIG9mZnNldD0iLjMxMyIgc3RvcC1jb2xvcj0iIzlkYTJhNiIgc3RvcC1vcGFjaXR5PSIwIi8+PHN0b3Agb2Zmc2V0PSIxIiBzdG9wLWNvbG9yPSIjOWRhMmE2Ii8+PC9yYWRpYWxHcmFkaWVudD48cGF0aCBmaWxsPSJ1cmwoI2NVRlJVUVdjbDhGek82ZWFqMGtFeGMpIiBkPSJNMTAsMTdjLTAuNTUyLDAtMSwwLjQ0OC0xLDFzMC40NDgsMSwxLDFzMS0wLjQ0OCwxLTFTMTAuNTUyLDE3LDEwLDE3eiIvPjxsaW5lYXJHcmFkaWVudCBpZD0iY1VGUlVRV2NsOEZ6TzZlYWowa0V4ZCIgeDE9IjIyLjMxMyIgeDI9IjI2LjYxOCIgeTE9IjE2LjA3OSIgeTI9IjQxLjQ0OCIgZ3JhZGllbnRVbml0cz0idXNlclNwYWNlT25Vc2UiPjxzdG9wIG9mZnNldD0iMCIgc3RvcC1jb2xvcj0iI2JhYzBmZiIvPjxzdG9wIG9mZnNldD0iLjM5OCIgc3RvcC1jb2xvcj0iIzhhOTFlNyIvPjxzdG9wIG9mZnNldD0iLjc5NiIgc3RvcC1jb2xvcj0iIzYwNjlkMSIvPjxzdG9wIG9mZnNldD0iMSIgc3RvcC1jb2xvcj0iIzUwNTljOSIvPjwvbGluZWFyR3JhZGllbnQ+PHBhdGggZmlsbD0idXJsKCNjVUZSVVFXY2w4RnpPNmVhajBrRXhkKSIgZD0iTTM1LDIxYy0wLjMzMSwwLTAuNjU3LDAuMDE4LTAuOTgsMC4wNDlDMzEuNzQxLDE3Ljk5LDI4LjEwOCwxNiwyNCwxNgljLTUuNzEzLDAtMTAuNTIsMy44MzctMTIuMDEyLDkuMDcxQzguMDQ5LDI1LjU2OSw1LDI4LjkyNSw1LDMzYzAsNC40MTgsMy41ODIsOCw4LDhjMS44MTgsMCwyMC4xMDcsMCwyMiwwYzUuNTIzLDAsMTAtNC40NzcsMTAtMTAJQzQ1LDI1LjQ3Nyw0MC41MjMsMjEsMzUsMjF6Ii8+PC9zdmc+" alt="">
          </div>
        </section>

      </div>
    </div>
    <div v-if="weatherData" class="map">
      <GoogleMap api-key="AIzaSyC2xhruq1ouPN061BnLWdvMCS-zkofR-5U" style="width: 100%; height: 500px" :center="center" :zoom="15">
        <Marker :options="markerOptions" />
      </GoogleMap>
    </div>
    <div v-if="weatherData" class="forecast">
      <h3>7 days forecast</h3>
      <div class="forecast__container">
        <div v-for="data in weatherData.daily" :key="data.dt">
          <div class="forecast__data">
            <p>{{ getDayName(convertToDate(data.dt))}}</p>
            <p>
              {{ 
                (data.temp.day - 273.15).toFixed()
              }}ºC
            </p>
            <p>
              {{ 
               data.weather[0].main
              }} 
              - 
              {{ 
                data.weather[0].description
              }}</p>
          </div>
        </div>
      </div>
    </div>
    <ModalComponent :open="isOpen" @close="isOpen = !isOpen" :weatherData="weatherData" />
  </div>
</template>

<!-- <script>
export default {

}
</script> -->
<script setup>
import VueGoogleAutocomplete from "vue-google-autocomplete";
import ModalComponent from './ModalComponent.vue'
import { ref, onMounted } from 'vue'
import axios from 'axios'
import LoadingComponent from './LoadingComponent.vue'
import { GoogleMap, Marker } from "vue3-google-map";

// reactive state
let weatherData = ref()
let location = ref()
const address = ref()
let isOpen = ref(false);
let isLoading = ref(false);
const center = ref({});
const markerOptions = { position: center, label: "L", title: "MAP POINT" };
// const center = { lat: 40.689247, lng: -74.044502 };


function convertToDate(val) {
  return new Date(val * 1000)
}

function getDayName(val) {
  let days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
  let d = new Date(val);
  return days[d.getDay()];
}

function getAddressData(addressData) {

  location.value = addressData.country

  const lat = addressData?.latitude
  const lon = addressData?.longitude
  center.value = {
    lat: addressData?.latitude, lng: addressData?.latitude
  }
  getData(lat, lon)
}

function getData(lat, lon) {
  isLoading.value = true
  const api_key = process.env.VUE_APP_API_KEY
  const url = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&appid=${api_key}`
  try {
    axios.get(url)
      .then(res => {
      weatherData.value = res.data
      isLoading.value = false
    })
  } catch (error) {
    isLoading.value = false
    return error
  }
}

// lifecycle hooks
onMounted(() => {
  navigator.geolocation.getCurrentPosition((position) => {
    center.value = {
      lat: position.coords.latitude, lng: position.coords.longitude
    }
    getData(position.coords.latitude, position.coords.longitude)
  });
})
</script>

<style lang="scss" scoped>
.weather {
  background: linear-gradient(180deg, #547BE4 0%, #7193E8 100%);
  color: #fff;
  &__container {
    padding: 30px 100px;
    @media screen and (max-width: 768px) {
      padding: 30px;
    }
    &__location {
      display: flex;
      align-items: center;
      gap: 10px;
      img {
        width: 20px;
      }
    }
    &__body {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 50px 0;
      &__left {
        display: flex;
        flex-direction: column;
        gap: 5px;
        h1 {
          font-size: 64px;
        }
        p {
          font-size: 16px;
          font-weight: 300;
        }
        button {
          color: #fff;
          background: transparent;
          border: none;
          text-align: left;
          margin-top: 20px;
          cursor: pointer;
        }
      }
      &__right {
        img {
          min-width: 200px;
        }
      }
    }
  }
  &__input {
    }
  .form-control {
    margin: 20px 0;
    min-width: 300px;
    padding: 10px;
    border-radius: 5px;
    margin-top: 10px;
    border: 1px solid #547BE4;
    // margin-top: 20px;
  }
}

.forecast {
  padding: 30px 100px;
  @media screen and (max-width: 768px) {
    padding: 30px;
  }
  h1 {
    font-size: 36px;
  }
  &__container {
    margin-top: 20px;
  }
  &__data {
    display: flex;
    gap: 20px;
    justify-content: space-between;
    max-width: 500px;
    padding: 10px 0;
    @media screen and (max-width: 768px) {
      flex-direction: column;
      border-bottom: 1px solid rgb(79, 76, 76);
    }
  }
}
</style>