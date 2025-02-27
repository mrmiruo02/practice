<template>
    <main class="container text-white">
        <div class="pt-4 mb-8 relative">
            <input 
                type="text" 
                v-model="search"
                @input="getQueryResults"
                placeholder="Enter for city" 
                class="py-2 px-1 w-full bg-transparent border-b 
                focus:border-weather-secondary focus:outline-none 
                focus: shadow-[0px_1px_0_0_#004E71]"
            />

            <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="apiSearchResults">
                <p v-if="searchError">
                    Sorry something went wrong. Please try again.
                </p>
                <p v-if="!serverError && apiSearchResults.length === 0">
                    No Results found. Please try a different search.
                </p>
                <template v-else>
                    <li v-for="searchResult in apiSearchResults"
                     :key="searchResult.id" class="py-2 cursor-pointer"
                     @click="previewCity(searchResult)"
                    >
                        {{ searchResult.place_name  }}
                    </li>
                </template>
            </ul>
        </div>
    </main>
</template>
<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';

    const search = ref('');
    const searchError = ref(null);
    const queryTimeOut = ref(null);
    const apiSearchResults = ref(null);
    const mapboxAPIKey =
        "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";

    const getQueryResults = () => {
        clearTimeout(queryTimeOut.value);
        queryTimeOut.value = setTimeout( async () => {
            if(search.value !== ''){
                try {
                    const result = await axios.get(
                        `https://api.mapbox.com/geocoding/v5/mapbox.places/${search.value}.json?access_token=${mapboxAPIKey}&types=place`
                    )
                    apiSearchResults.value = result.data.features;
                } catch {
                    searchError.value = true;
                }

                return;
            }
            apiSearchResults.value = null;
        }, 300);
    }

    const router = useRouter();
    const previewCity = (searchResult) => {
        const [city, state] = searchResult.place_name.split(',');
        router.push({
            name: 'cityView',
            params: { state: state.replaceAll(" ", ""), city: city  },
            query: { 
                lat: searchResult.geometry.coordinates[1],
                lng: searchResult.geometry.coordinates[0],
                preview: true,
            }
        });
    }
</script>