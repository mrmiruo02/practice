<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>
                You are currently previewing this city, click the "+"
                icon to start tracking this city.
            </p>
        </div>
        <!-- Weather Overview -->
         <div class="flex flex-col text-white items-center py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{
                    new Date(weatherData.data.currentTime).toLocaleDateString(
                        "en-us",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{
                    new Date(weatherData.data.currentTime).toLocaleTimeString(
                        "en-us",
                        {
                            timeStyle: "short",
                        }
                    )
                }}  
            </p>
            <p class="text-8xl mb-8">
                {{ Math.round((weatherData.data.main.temp - 32)* 5/9) }}°C
            </p>
            <p>
                Feels like
                {{ Math.round((weatherData.data.main.feels_like - 32)* 5/9) }} &deg;
            </p>
            <p class="capitalize">
                {{ weatherData.data.weather[0].description }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="
                `http://openweathermap.org/img/wn/${weatherData.data.weather[0].icon}@2x.png`
              "
              alt=""
            />
         </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';
const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherDataResults = await axios.get(
            `https://pro.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=fc84e567667b28a5a8c9e9cff3b6acd9&units=imperial`
        );

        // cal current date & time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherDataResults.data.dt * 1000 + localOffset;
        weatherDataResults.data.currentTime =
        utc + 1000 * weatherDataResults.data.timezone;
        
        return weatherDataResults;

    } catch (error){
        console.log(error);
    }
};

const weatherData = await getWeatherData();
console.log(weatherData);

</script>