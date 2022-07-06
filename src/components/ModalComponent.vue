<template>
  <transition name="fade">
    <div class="vue-modal" v-show="open">
      <transition name="drop-in">
        <div class="vue-modal-inner"  v-show="open">
          <div class="vue-modal-content">
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
            <p>Wind Speed - {{ weatherData?.current.wind_speed }}</p>
            <p>Humidity - {{ weatherData?.current.humidity }}</p>
            <p>Pressure - {{ weatherData?.current.pressure }}</p>
            <p>Sunrise/Sunset Time - 
              {{ convertToDate(weatherData?.current.sunrise).getHours()}}:
              {{ convertToDate(weatherData?.current.sunrise).getMinutes()}} / 
              {{ convertToDate(weatherData?.current.sunset).getHours()}}:
              {{ convertToDate(weatherData?.current.sunset).getMinutes()}}
            </p>
            <button type="button" @click="$emit('close')">Close</button>
          </div>
        </div>
      </transition>
    </div>
  </transition>

</template>

<script>

export default {
  props: {
    open: {
      type: Boolean,
      default: true,
    },
    weatherData: {
      type: Object
    }
  },
  methods: {
    convertToDate(val) {
      return new Date(val * 1000)
    }
  },
  
};
</script>

<style lang="scss" scoped>
.vue-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow-x: hidden;
  overflow-y: auto;
  background-color: rgba(0, 0, 0, 0.4);
  z-index: 1;
}

.vue-modal-inner {
  max-width: 300px;
  margin: 2rem auto;
}

.vue-modal-content {
  position: relative;
  background-color: #fff;
  border: 1px solid rgba(0, 0, 0, 0.3);
  background-clip: padding-box;
  border-radius: 0.3rem;
  padding: 1rem;
  color: #000;
  h1 {
    font-size: 56px;
    margin: 10px 0;
  }
  p {
    font-size: 14px;
    margin: 5px 0;
  }
  button {
    margin-top: 20px;
    background: rgba(0, 0, 0, 0.3);
    color: #fff;
    border-radius: 10px;
    padding: 5px 10px;
    border: none;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.drop-in-enter-active,
.drop-in-leave-active {
  transition: all 0.3s ease-out;
}

.drop-in-enter-from,
.drop-in-leave-to {
  opacity: 0;
  transform: translate(0, -50px);
}
</style>