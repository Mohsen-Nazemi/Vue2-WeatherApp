<template>
  <div class="bodyBg" id="main">
    <div id="mainContainer" class="mb-3">
      <div class="container my-5">
        <h1 class="title text-center mb-5">اپلیکیشن آب و هوا</h1>
        <form class="search-location w-50 mx-auto" v-on:submit.prevent="getWeather">
          <input
            ref="cityInput"
            type="text"
            class="form-control  form-rounded p-4 shadow-sm"
            placeholder="نام شهر را وارد کنید"
            v-model="citySearch"
            autocomplete="off"
          />
        </form>
              <div class="notFound text-center my-3 alert alert-danger" v-if="cityFound">شهر مورد نظر شما متاسفانه پیدا نشد</div>

        <div v-show="visible" class="card my-3 mx-auto" style="max-width: 540px" >
          <div class="row no-gutters">
            <div class="col-md-4">
              <img :src="require(`./assets/icons/${weatherMod}.svg`)" class="card-img" />
            </div>
            <div class="col-md-8">
              <div class="card-body">
                <h3 class="card-title">
                  {{weather.cityName}}
                  <span  class="rounded-circle badge-info  p-2 mr-2">{{isDay?"روز":"شب"}}</span>
                  </h3>
                <h2 class="card-title">{{ weather.temperature }}&deg;C</h2>
                <h3 class="card-title">{{weather.description}}</h3>
                <p>
                 کمترین درجه {{ weather.lowTemp }}&deg;C
                </p>
                <p>
                 بیشترین درجه {{ weather.highTemp }}&deg;C
                </p>
                <h4 class="card-title">رطوبت {{weather.humidity}}%</h4>
                <h5 class="card-title">دمایی که احساس میشود {{weather.feelsLike}}%</h5>

                
                <p class="card-text">
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      weatherMod:"clearSky",
      cityFound: false,
      visible: false,
      

      isDay: true,
      citySearch: "",
      weather: {
        cityName: "Gwarinpa",
        country: "NG",
        temperature: 12,
        description: "Clouds everywhere",
        lowTemp: "19",
        highTemp: "30",
        feelsLike: "20",
        humidity: "55",
      },
    };
  },
  mounted() {
    this.focusInput();
  },
  methods: {
    getWeather: async function () {
            
      console.log(this.weatherMod);
      console.log(this.citySearch);
      const key = "5299e83c9cf2fef510fb8ddaa3206ac8";
      const baseURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&lang=fa&appid=${key}&units=metric`;

      //fetch weather
      try {
        const response = await fetch(baseURL);
        const data = await response.json();
        console.log(data);
        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = Math.round(data.main.humidity);

        const timeOfDay = data.weather[0].icon;

        ///check for time of day
        if (timeOfDay.includes("n")) {
          this.isDay = false;
        } else {
          this.isDay = true;
        }

        const mainWeather = data.weather[0].main;
        //check weather animations
        if (mainWeather.includes("Clear") && this.isDay) {
          this.weatherMod="clearSky"
        }
        if (mainWeather.includes("Clear") && !this.isDay) {
          this.weatherMod="clearNight"
        }
        if (mainWeather.includes("Clouds")) {
          this.weatherMod="clouds"
        }
        if (
          mainWeather.includes("Thunderstorm")
        ) {
          this.weatherMod="thunder"
        }
        
        
        if (mainWeather.includes("Rain")) {
          this.weatherMod="weahter_icon_rainy"
        }
        if (mainWeather.includes("Snow")) {
          this.weatherMod="snow"
        }
        this.visible = true;
        this.cityFound = false;
      } catch (error) {
        console.log(error);
        this.cityFound = true;
        this.visible = false;
      }
    },
    focusInput() {
      this.$refs.cityInput.focus();
    }
  },
};
</script>

<style scoped>
@import "./assets/custom.css";
</style>
