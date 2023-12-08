
<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text"
      @input="getSearchResult"
      v-model="searchQuery"
      placeholder="Search for a city or state" 
      class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary
      focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
      <ul class="absolute bg-weather-secondary text-white w-full py-2 px-1 top-[66px]" v-if="mapBoxSearchResults">
        <p class="py-2" v-if="!searchError && mapBoxSearchResults.length === 0">No results match</p>
        <li v-for="searchResult in mapBoxSearchResults" :key="searchResult.id" class="cursor-pointer py-1 px-1 hover:bg-slate-500" 
        @click="previewCity(searchResult)">
          {{ searchResult.place_name }}
        </li>
      </ul>
      <p v-if="searchError" class="py-2 px-1">Sorry something went wrong</p>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from "axios";
import { useRouter } from 'vue-router';

const router = useRouter();

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const searchQuery = ref("")
const queryTimeout = ref(null);
const mapBoxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResult = () => {
  clearTimeout(queryTimeout.value);
    queryTimeout.value = setTimeout( async () => {
      if(searchQuery !== "") {
        try {
          const result = await axios.get(
            `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=locality`
          );
          mapBoxSearchResults.value = result.data.features;
        } catch {
          searchError.value = true;
        }
        return;
      }
      mapBoxSearchResults.value = null;
    }, 300);
    console.log(queryTimeout.value)
}

const previewCity = (searchResult) => {
  console.log(searchResult)
  const [city, state] = searchResult.place_name.split(',')
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    }
  });
  console.log(city, state)
}
</script>
