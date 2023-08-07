<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner-->
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>
                You are currently previewing this city, click the "<b>+</b>" icon to start tracking this city.
            </p>
        </div>
        <!-- Weather Overview-->
        <div class="flex flex-col items-center text-white text-center py-12">
            <h1 class="text-4xl mb-2">
                {{ route.params.city }}
            </h1>
            <p class="text-sm mb-12">
                    {{
                        new Date(weatherData.currentTime).toLocaleDateString(
                            "en-us",
                            {
                                weekday: "short",
                                day: "2-digit",
                                month: "long",
                                year: "numeric",
                                hour: "numeric",
                                minute: "numeric",
                            }
                        )
                    }}
                </p>
                <p class="text-8xl mb-8">
                    {{ Math.round(weatherData.current.temp) }}&deg; F
                </p>
                <p>
                    Feels Like
                    <b>
                        {{ Math.round(weatherData.current.feels_like) }}&deg; F
                    </b>
                </p>
                <p class="capitalize">
                    {{ weatherData.current.weather[0].description }}
                </p>
                <img
                class="w-[150px] h-auto"
                :src=" `http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png` "
                />
        </div>

        <hr class="border-white border-opacity-10 border w-full"/>
        <!-- More Weather Info-->

        <div class="flex flex-col items-center text-white text-center py-12">
            <h2 class="text-2xl mb-2">More Info</h2>
                <p class="m-auto">
                    Clouds: {{ weatherData.current.clouds }} %
                    &nbsp;
                    Dew Point: {{ Math.round(weatherData.current.dew_point) }}&deg F
                </p>
                <p class="m-auto gap-2">
                    Humidity: {{ weatherData.current.humidity }} %
                    &nbsp;
                    Pressure: {{ weatherData.current.pressure }} mbar
                </p>
                <p class="m-auto">
                    UV Index: {{ weatherData.current.uvi }}
                    &nbsp;
                    Visibility: {{ weatherData.current.visibility / 1000 }} km
                </p>
                <p class="m-auto">
                    Wind Speed: {{ weatherData.current.wind_speed}} km/h
                    &nbsp;
                    Wind Degree: {{ weatherData.current.wind_deg }}
                </p>
        </div>

        <hr class="border-white border-opacity-10 border w-full"/>
        <!-- Hourly Weather-->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="text-2xl text-center mb-4">Hourly Weather</h2>
                <div class="flex gap-10 overflow-x-scroll">
                    <div v-for="hourData in weatherData.hourly" :key="hourData.dt" class="flex flex-col gap-4 items-center">
                        <p class="whitespace-nowrap text-md">
                            {{
                                new Date(hourData.currentTime).toLocaleTimeString(
                                    "en-us",
                                    { hour: "numeric",
                                })
                            }}
                        </p>
                        <img
                            class="w-auto h-[50px] object-cover"
                            :src=" `http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png` "
                        />
                        <p class="text-xl">
                            {{ Math.round(hourData.temp) }}&deg;
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';

const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(
            `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
            );
            
        //calculate current date and time 
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;

        //calculate hourly weather offset
        weatherData.data.hourly.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
        });

        return weatherData.data;
    } catch (err) {
        console.log(err);
    }
};

const weatherData = await getWeatherData();
console.log(weatherData);
</script>
