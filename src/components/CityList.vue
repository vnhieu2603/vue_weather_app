<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from "axios";
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';

const savedCities = ref([]);
const getCities = async () => {
    if(localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));

        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=808031dedcb82e5b8b6f616f74d271b3&units=imperial`)
            )
        });

        const weatherData = await Promise.all(requests);
        weatherData.forEach((value, index) => {
            savedCities.value[index].weatherData = value.data;
        })
    }
}

await getCities();

const router = useRouter();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: {
      id: city.id, 
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
}
</script>