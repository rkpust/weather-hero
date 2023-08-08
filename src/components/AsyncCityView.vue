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
                <div class="mb-8 ">
                    <span class="text-8xl mb-8"
                    v-if="fahrenheit"
                    >
                        {{ Math.round(weatherData.current.temp) }}&deg;
                    </span>
                    <span class="text-8xl mb-8" v-if="celsius">
                        {{ Math.round((weatherData.current.temp - 32) / 1.8000) }}&deg;
                    </span>
                    <span 
                        class="text-8xl cursor-pointer" 
                        :class="{'text-white': (fahrenheit === true && (celsius === null || celsius === false)),
                        'text-weather-secondary': (fahrenheit === false && celsius === true)}"
                        @click="fahrenheitTemp"
                    >F</span>
                    <span class="text-8xl text-weather-secondary">|</span>
                    <span 
                        class="text-8xl cursor-pointer" 
                        :class="{'text-white': (celsius === true && fahrenheit === false), 
                        'text-weather-secondary': ((celsius === false || celsius === null) && fahrenheit === true)}" 
                        @click="celsiusTemp"
                    >C</span>
            </div>
                <p>
                    Feels Like
                    <b v-if="fahrenheit">
                        {{ Math.round(weatherData.current.feels_like) }}&deg; F
                    </b>
                    <b v-if="celsius">
                        {{ Math.round((weatherData.current.feels_like - 32) / 1.8000) }}&deg; C
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

        <div class="flex flex-col items-center text-white text-center py-12 gap-2">
            <h2 class="text-2xl mb-2">More Weather Info</h2>
                <p>
                    Clouds: {{ weatherData.current.clouds }} % 
                </p>
                <p v-if="fahrenheit">
                    Dew Point: {{ Math.round(weatherData.current.dew_point) }}&deg; F
                </p>
                <p v-if="celsius">
                    Dew Point: {{ Math.round((weatherData.current.dew_point - 32) / 1.8000) }}&deg; C
                </p>
                <p>
                    Humidity: {{ weatherData.current.humidity }} %
                </p>
                <p>
                    Pressure: {{ weatherData.current.pressure }} mbar 
                </p>
                <p>
                    UV Index: {{ weatherData.current.uvi }}
                </p>
                <p>
                    Visibility: {{ weatherData.current.visibility / 1000 }} km
                </p>
                <p>
                    Wind Speed: {{ weatherData.current.wind_speed}} km/h
                </p>
                <p>
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
                        <p class="text-xl" v-if="fahrenheit">
                            {{ Math.round(hourData.temp) }}&deg;
                        </p>
                        <p class="text-xl" v-if="celsius">
                            {{ Math.round((hourData.temp - 32) / 1.8000) }}&deg;
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <hr class="border-white border-opacity-10 border w-full"/>
        <!-- Daily Weather-->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="text-2xl text-center mb-4">7 Day Forecast</h2>
                <div 
                    v-for="day in weatherData.daily" 
                    :key="day.dt"
                    class="flex items-center"
                    >
                    <p class="flex-1">
                        {{ new Date(day.dt * 1000).toLocaleDateString(
                            "en-us",
                            {
                                weekday: "long",
                            }
                        ) }}
                    </p>
                    <img
                    class="w-[50px] h-[50px] object-cover"
                    :src=" `http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png` "
                    />
                    <div class="flex gap-2 flex-1 justify-end">
                        <p v-if="fahrenheit">H: {{ Math.round(day.temp.max) }}&deg;</p>
                        <p v-if="fahrenheit">L: {{ Math.round(day.temp.min) }}&deg;</p>
                        <p v-if="celsius">H: {{ Math.round((day.temp.max - 32) / 1.8000) }}&deg;</p>
                        <p v-if="celsius">L: {{ Math.round((day.temp.min - 32) / 1.8000) }}&deg;</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';
import { ref } from 'vue';

const celsius = ref(null);
const fahrenheit = ref(true);

const celsiusTemp = () => {
    celsius.value = true;
    fahrenheit.value = false;
}

const fahrenheitTemp = () => {
    fahrenheit.value = true;
    celsius.value = false;
}

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
