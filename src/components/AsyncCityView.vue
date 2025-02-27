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
                    new Date(weatherData.current.time).toLocaleDateString(
                        "en-us",
                        {
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{
                    new Date(weatherData.current.time).toLocaleTimeString(
                        "en-us",
                        {
                            timeStyle: "short",
                        }
                    )
                }}  
            </p>
            <p class="text-8xl mb-8">
                {{ Math.round(weatherData.hourly.temperature_2m[0]) }}°C
            </p>
            <!-- <div class="text-center">
                <p class="capitalize">
                    {{ weatherData. }}
                </p>
            </div> -->
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
            `https://api.open-meteo.com/v1/forecast?latitude=${route.query.lat}&longitude=${route.query.lng}&current=temperature_2m,wind_speed_10m&hourly=temperature_2m,relative_humidity_2m,wind_speed_10m`
        );

        return weatherDataResults.data;

    } catch (error){
        console.log(error);
    }
};

const weatherData = await getWeatherData(); 
console.log(weatherData);

</script>