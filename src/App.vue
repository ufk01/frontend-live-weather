<template>
    <div class="weather-app">
        <header class="header">
            <h1>{{this.languageEn.header}}</h1>
        </header>
        <div class="selection-container">
            <form @submit.prevent="fetchWeatherData" class="weather-form">
                <div class="form-group">
                    <label for="country">{{languageEn.weather.country}}:</label>
                    <select id="country" v-model="selectedCountry" @change="handleCountryChange">
                        <option v-for="country in countries" :key="country.name" :value="country.value">
                            {{ country.name }}
                        </option>
                    </select>
                </div>
                <div class="form-group">
                    <label for="city">{{languageEn.weather.city}}:</label>
                    <select id="city" v-model="selectedCity" @change="handleCityChange">
                        <option v-for="city in cities" :key="city.name" :value="city.value">
                            {{ city.name }}
                        </option>
                    </select>
                </div>
                <div class="form-group">
                    <label for="time-interval">{{languageEn.timeInterval}}:</label>
                    <select id="time-interval" v-model="selectedTimeInterval" @change="handleTimeIntervalChange">
                        <option v-for="interval in timeIntervals" :key="interval.label" :value="interval.value">
                            {{ interval.label }}
                        </option>
                    </select>
                </div>
                <div class="button-container">
                    <button @click="resetToDefault" class="action-button">{{languageEn.resetButton}}</button>
                    <button @click="clearTable" class="action-button">{{languageEn.clearButton}}</button>
                </div>
            </form>

        </div>

        <div class="weather-table-container" v-if="!validation(weatherResponseDto.city) && !validation(weatherResponseDto.country) && !loadingTable">
            <table class="weather-table">
                <thead>
                <tr>
                    <th>{{languageEn.currentTime}}</th>
                    <th>{{languageEn.weather.time}}</th>
                    <th>{{languageEn.weather.country}}</th>
                    <th>{{languageEn.weather.city}}</th>
                    <th>{{languageEn.weather.temperature}}</th>
                    <th>{{languageEn.weather.humidity}} </th>
                    <th>{{languageEn.weather.wind}}</th>
                    <th>{{ languageEn.weather.description }}</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(data, index) in previousInfo" :key="index">
                    <td>{{ data.localDateTime }}</td>
                    <td>{{ convertTime(data.dt) }}</td>
                    <td>{{data.country}}</td>
                    <td>{{ data.city }}</td>
                    <td>{{ convertKelvinToCelsius(data.temp) }}</td>
                    <td>{{ data.humidity }}</td>
                    <td>{{ data.windSpeed }}</td>
                    <td>{{ data.description }}</td>
                </tr>
                </tbody>
            </table>
        </div>

        <Popup position="top-left">
            <p style="text-align: center"><strong>Hint1</strong></p>
               <p>When input fields are changed, the current interval is cleared and the latest data is processed;
                   then, data is updated according to the new configuration.</p>
        </Popup>
        <Popup position="top-right">
            <p style="text-align: center"><strong>Hint2</strong></p>
            <p><strong>Reset Input:</strong> This resets the input fields to default values (Turkey/Ankara/5 Seconds).</p>
            <p><strong>Clear Table:</strong> This clears the data in the table.</p>
            <p>Default data (e.g., Turkey/Ankara) is fetched from the backend configuration (.yml file). Other data (e.g., Germany/Munich) is provided by the user.</p>
        </Popup>
    </div>
</template>
<script>
import axios from "axios";
import { DateTime } from 'luxon';
import Popup from './components/Popup.vue';

export default {
    name: 'WeatherApp',
    data() {
        return {
            countries: [
                {name: "Default (Turkey)", value: null},
                {name: "United States", value: "United States"},
                {name: "Germany", value: "Germany"}
            ],
            cities: [],
            selectedTimeInterval: 5000,
            timeIntervals: [
                {label: "5 Seconds", value: 5000},
                {label: "10 Seconds", value: 10000},
                {label: "30 Seconds", value: 30000},
                {label: "1 Minute", value: 60000}
            ],
            selectedCountry: null,
            selectedCity: null,
            weatherResponseDto: {
                temp:'',
                humidity: '',
                pressure: '',
                country: '',
                description: '',
                city: '',
                windSpeed: '',
                dt: '',
                localDateTime: '',
            },
            languageEn: {
                header: 'Live Weather',
                button: 'Get Weather',
                currentTime: "Current Time",
                timeInterval: "Time Interval",
                resetButton: "Reset Inputs",
                clearButton: "Clear Table",
                weather: {
                    temperature: 'Temperature (°C)',
                    wind: 'Wind (km/h)',
                    state: 'Condition',
                    humidity: "Humidity (%)",
                    city: 'City',
                    country: 'Country',
                    time: 'Latest Live Data Update',
                    feelsLike: 'Feels Like',
                    description: "Description",
                }
            },
            loadingTable: false,
            previousInfo: [],
            interval: null,
            props: {
                position: {
                    type: String,
                    default: 'top-right'
                }
            },

        };
    },
    components: {
        Popup
    },
    methods: {
        convertTime(unixTimestamp){
            const date = new Date(unixTimestamp * 1000);
            return date.toLocaleString();
        },
        convertKelvinToCelsius(kelvin){
            return (kelvin - 273.15).toFixed(2);
        },

        validation(obj){
            return obj === '' || obj === null || obj === undefined;
        },
        fetchCities() {
            if(this.selectedCountry === null){
                this.cities = [
                    {name: "Default (Ankara)", value: null},
                    {name: "İstanbul", value: "İstanbul"},
                    {name: "İzmir", value: "İzmir"}
                ];
            }
            if (this.selectedCountry === "Turkey") {
                this.cities = [
                    {name: "Default (Ankara)", value: null},
                    {name: "İstanbul", value: "İstanbul"},
                    {name: "İzmir", value: "İzmir"}
                ];
            } else if (this.selectedCountry === 'United States') {
                this.cities = [
                    {name: "New York", value: "New York"},
                    {name: "Los Angeles", value: "Los Angeles"},
                    {name: "Chicago", value: "Chicago"}
                ];
            } else if (this.selectedCountry === 'Germany') {
                this.cities = [
                    {name: "Berlin", value: "Berlin"},
                    {name: "Munich", value: "Munich"},
                    {name: "Hamburg", value: "Hamburg"}
                ];
            }
            else {
                this.selectedCity = null;
            }
        },
        startInterval(duration, exp) {
            if(!exp === true) {
                this.fetchWeatherData();
            }
            this.interval = setInterval(() => this.fetchWeatherData(), duration);
        },
        stopInterval() {
            clearInterval(this.interval);
        },
        handleCountryChange() {
            this.fetchCities();
            this.resetInterval();
        },
        handleCityChange() {
            this.resetInterval();
        },
        handleTimeIntervalChange() {
            this.resetInterval();
        },
        resetInterval(exp) {
            this.stopInterval();
            this.startInterval(this.selectedTimeInterval, exp);
        },
         fetchWeatherData() {
            this.loadingTable = true;
             axios
                .get("http://localhost:5005/weather/information", {
                    params: {
                        city: this.selectedCity,
                        country: this.selectedCountry
                    }
                })
                .then(response => {
                    this.weatherResponseDto = response.data;
                    this.previousInfo.unshift(this.weatherResponseDto)
                    if (this.previousInfo.length > 5) {
                        this.previousInfo.splice(5);
                    }
                    const originalDate = DateTime.local().toFormat('yyyy-MM-dd HH:mm:ss');
                    const [date, time] = originalDate.split(' ');
                    const [year, month, day] = date.split('-');
                    this.weatherResponseDto.localDateTime = `${day}.${month}.${year} ${time}`;
                })
                .catch(error => {
                    console.error("Weather data fetch error:", error);
                });
            this.loadingTable = false;
        },
        resetToDefault() {
            this.selectedCountry = null;
            this.selectedCity = null;
            this.fetchCities();
            this.selectedTimeInterval = 5000;
            this.resetInterval(true);
        },
        clearTable() {
            this.previousInfo = [];
            this.resetInterval(true);
        }
    },
    mounted(){
        this.fetchCities();
        this.startInterval(5000);
    },
}

</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

.weather-app {
    width: 100%;
    height: 98vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: 'Roboto', sans-serif;
    overflow-y: hidden;
    position: relative;
}

.weather-app::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: url('@/assets/background.jpg') no-repeat center center;
    background-size: cover;
    opacity: 1;
    z-index: -1;
}

.header {
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    background: #007bff;
    color: #fff;
    text-align: center;
    border-bottom-left-radius: 30px;
    border-bottom-right-radius: 30px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
    margin-bottom: 40px;
    opacity: 0.88;
}

h1 {
    font-size: 2.5em;
    margin: 0;
    font-weight: 700;
}

.selection-container {
    width: 100%;
    max-width: 1230px;
    background: #fff;
    border-radius: 15px;
    padding: 20px;
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
    margin-bottom: 30px;
    border: 1px solid #ddd;
    background: rgba(255, 255, 255, 0.8);
}
.weather-table-container {
    background: rgba(255, 255, 255, 0.8);
    opacity: 0.89;
}
.weather-form {
    display: flex;
    flex-direction: column;
}

.form-group {
    margin-bottom: 20px;
}

label {
    display: block;
    font-size: 1.1em;
    margin-bottom: 8px;
    color: #333;
    font-weight: 600;
}

select {
    width: 100%;
    padding: 10px;
    font-size: 1em;
    border: 1px solid #ddd;
    border-radius: 8px;
    background-color: #f5f5f5;
    transition: border-color 0.3s, background-color 0.3s;
}

select:focus {
    border-color: #007bff;
    background-color: #fff;
    outline: none;
}

button {
    width: 100%;
    padding: 12px;
    font-size: 1.1em;
    color: #fff;
    background-color: #007bff;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s, box-shadow 0.3s;
}

button:hover {
    background-color: #0056b3;
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
}

.weather-table-container {
    width: 100%;
    max-width: 1230px;
    background: #fff;
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
    margin-top: 1px;
    max-height: 500px;
    overflow-y: auto;
}


.weather-table-container h2 {
    font-size: 2em;
    margin-bottom: 8px;
    color: #333;
    text-align: center;
    font-weight: 700;
}

.weather-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 1em;
    color: #333;
}

.weather-table th,
.weather-table td {
    padding: 15px;
    text-align: center;
    border-bottom: 1px solid #ddd;
}

.weather-table th {
    background-color: #007bff;
    color: #fff;
    font-weight: 700;
}

.weather-table tbody tr:nth-child(even) {
    background-color: #f9f9f9;
}

.weather-table tbody tr:nth-child(odd) {
    background-color: #fff;
}

.button-container {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
    opacity: 0.88;

}

.action-button {
    padding: 10px 20px;
    font-size: 1em;
    color: #fff;
    background-color: #007bff;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s, box-shadow 0.3s;
}

.action-button:hover {
    background-color: #0056b3;
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
}

</style>
