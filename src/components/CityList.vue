<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard  :city="city" />
    </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import CityCard from "../components/CityCard.vue";

const savedCities = ref([]);
const getCities = async () =>{
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));

        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`)
                );
        });

        const weatherData = await Promise.all(requests);
        
        weatherData.forEach((val, index) => {
            savedCities.value[index].weather = val.data;
        });
      console.log(savedCities.value);
    }
};

await getCities();
</script>