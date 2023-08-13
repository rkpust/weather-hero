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
                <i class="fa-solid fa-location-dot"></i> {{ route.params.city }}
            </h1>
            <p class="text-sm mb-12">
                <i class="fa-solid fa-calendar-days"></i> 
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
                    ( {{ weatherData.timezone }} )
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

                <div class="flex flex-row items-center gap-4">
                    <img class="w-[50px] h-[50px] mt-2" src="/image/sunrise.png" />
                    {{
                        new Date(weatherData.sunRise).toLocaleTimeString(
                            "en-us",
                            {
                                hour: "numeric",
                                minute: "numeric",
                                second: "numeric",
                            }
                        )
                    }}

                    <img class="w-[50px] h-[50px] ml-2" src="/image/sunset.png" />
                    {{
                        new Date(weatherData.sunSet).toLocaleTimeString(
                            "en-us",
                            {
                                hour: "numeric",
                                minute: "numeric",
                                second: "numeric",
                            }
                        )
                    }}
                </div>
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
                    Wind Gust: {{ weatherData.current.wind_gust}} m/s
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
                <div class="flex overflow-x-scroll">
                    <table class="border-collapse border border-white border-opacity-10 mb-8">
                        <tr class="py-4 text-center">
                            <th 
                                class="border border-white border-opacity-10 whitespace-nowrap text-md">
                                Time :
                            </th>
                            <th 
                                v-for="hourData in weatherData.hourly" 
                                :key="hourData.dt" 
                                class="whitespace-nowrap text-md border border-white border-opacity-10"
                            >
                                {{
                                    new Date(hourData.currentTime).toLocaleTimeString("en-us",{ hour: "numeric"})
                                }}
                            </th>
                        </tr>
                        <tr class="py-4 text-center ">
                            <th 
                                class="border border-white border-opacity-10 whitespace-nowrap text-md"
                            >
                                Image :
                            </th>
                            <td 
                                v-for="hourData in weatherData.hourly" 
                                :key="hourData.dt" 
                                class="whitespace-nowrap text-sm border border-white border-opacity-10"
                            >
                                <img
                                    class="w-[50px] h-[50px] object-cover mx-auto"
                                    :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
                                />
                            </td>
                        </tr>
                        <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10 whitespace-nowrap text-md"
                        >
                            Weather :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm text-center capitalize border border-white border-opacity-10"
                        >
                            {{ hourData.weather[0].description }}
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10 whitespace-nowrap text-md"
                        >
                            Temperature :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10" v-if="fahrenheit"
                        >
                            {{ Math.round(hourData.temp) }}&deg; F
                        </td>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10" 
                            v-if="celsius"
                        >
                            {{ Math.round((hourData.temp - 32) / 1.8000) }}&deg; C
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10 whitespace-nowrap text-md"
                        >
                            Feels Like :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10" 
                            v-if="fahrenheit"
                        >
                            {{ Math.round(hourData.feels_like) }}&deg; F
                        </td>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10" 
                            v-if="celsius"
                        >
                            {{ Math.round((hourData.feels_like - 32) / 1.8000) }}&deg; C
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            Clouds :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            {{ hourData.clouds}} %
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            Humidity :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-md border border-white border-opacity-10"
                        >
                            {{ hourData.humidity}} %
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            Dew Point:
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10" 
                            v-if="fahrenheit"
                        >
                            {{ Math.round(hourData.dew_point) }}&deg; F
                        </td>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10" 
                            v-if="celsius"
                        >
                            {{ Math.round((hourData.dew_point - 32) / 1.8000) }}&deg; C
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            Pressure :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            {{ hourData.pressure}} hPa
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            Visibility :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            {{ hourData.visibility / 1000 }} km
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            Wind Speed :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            {{ hourData.wind_speed }} km
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                        class="border border-white border-opacity-10"
                        >
                            Wind Gust :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" 
                            class="whitespace-nowrap text-sm border border-white border-opacity-10"
                            >
                            {{ hourData.wind_gust }} m/s
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            WindDirection:
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt"
                            class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            <span v-if="(hourData.wind_deg >= 0.0 && hourData.wind_deg <= 11.25) || (hourData.wind_deg >= 348.76 && hourData.wind_deg <= 360.0)">N,</span>
                            <span v-if="hourData.wind_deg >= 11.26 && hourData.wind_deg <= 33.75" >NNE,</span>
                            <span v-if="hourData.wind_deg >= 33.76 && hourData.wind_deg <= 56.25" >NE,</span>
                            <span v-if="hourData.wind_deg >= 56.26 && hourData.wind_deg <= 78.75" >ENE,</span>
                            <span v-if="hourData.wind_deg >= 78.76 && hourData.wind_deg <= 101.25" >E,</span>
                            <span v-if="hourData.wind_deg >= 101.26 && hourData.wind_deg <= 123.75" >ESE,</span>
                            <span v-if="hourData.wind_deg >= 123.76 && hourData.wind_deg <= 146.25" >SE,</span>
                            <span v-if="hourData.wind_deg >= 146.26 && hourData.wind_deg <= 168.75" >SSE,</span>
                            <span v-if="hourData.wind_deg >= 168.76 && hourData.wind_deg <= 191.25" >S,</span>
                            <span v-if="hourData.wind_deg >= 191.26 && hourData.wind_deg <= 213.75" >SSW,</span>
                            <span v-if="hourData.wind_deg >= 213.76 && hourData.wind_deg <= 236.25" >SW,</span>
                            <span v-if="hourData.wind_deg >= 236.26 && hourData.wind_deg <= 258.75" >WSW,</span>
                            <span v-if="hourData.wind_deg >= 258.76 && hourData.wind_deg <= 281.25" >W,</span>
                            <span v-if="hourData.wind_deg >= 281.26 && hourData.wind_deg <= 303.75" >WNW,</span>
                            <span v-if="hourData.wind_deg >= 303.76 && hourData.wind_deg <= 326.25" >NW,</span>
                            <span v-if="hourData.wind_deg >= 326.26 && hourData.wind_deg <= 348.75" >NNW,</span>
                            {{ hourData.wind_deg }}&deg;
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                            >
                                UV Index :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            {{ hourData.uvi }} 
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                        class="border border-white border-opacity-10"
                        >
                            PoP :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            {{ hourData.pop}}
                        </td>
                    </tr>
                    <tr class="py-4 text-center">
                        <th 
                            class="border border-white border-opacity-10"
                        >
                            Rain Rate :
                        </th>
                        <td 
                            v-for="hourData in weatherData.hourly" 
                            :key="hourData.dt" class="whitespace-nowrap text-sm border border-white border-opacity-10"
                        >
                            {{ hourData.rain }} mm
                        </td>
                    </tr>
                </table>
            </div>
        </div>
    </div>

        <hr class="border-white border-opacity-10 border w-full"/>
        <!-- Daily Weather-->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="text-2xl text-center mb-4">8 Day Forecast</h2>
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

        //calculate sunrise date and time 
        const UTC = weatherData.data.current.sunrise * 1000 + localOffset;
        weatherData.data.sunRise = UTC + 1000 * weatherData.data.timezone_offset;
        
        //calculate sunset date and time 
        const UtC = weatherData.data.current.sunset * 1000 + localOffset;
        weatherData.data.sunSet = UtC + 1000 * weatherData.data.timezone_offset;
        });

        return weatherData.data;
    } catch (err) {
        console.log(err);
    }
};

const weatherData = await getWeatherData();
console.log(weatherData);
</script>
