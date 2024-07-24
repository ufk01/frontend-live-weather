<template>
    <div class="weather-app">
        <header class="header">
            <h1>Canlı Hava Durumu</h1>
        </header>
        <div class="selection-container">
            <form @submit.prevent="fetchWeatherData" class="weather-form">
                <div class="form-group">
                    <label for="country">Ülke:</label>
                    <select id="country" v-model="selectedCountry" @change="fetchCities">
                        <option v-for="country in countries" :key="country" :value="country">
                            {{ country }}
                        </option>
                    </select>
                </div>
                <div class="form-group">
                    <label for="city">Şehir:</label>
                    <select id="city" v-model="selectedCity">
                        <option v-for="city in cities" :key="city" :value="city">
                            {{ city }}
                        </option>
                    </select>
                </div>
                <button type="submit">Hava Durumunu Getir</button>
            </form>
        </div>

        <div class="weather-table-container" v-if="weatherData">
            <h2>{{ selectedCity }}, {{ selectedCountry }} Hava Durumu</h2>
            <table class="weather-table">
                <thead>
                <tr>
                    <th>Şehir</th>
                    <th>Sıcaklık (°C)</th>
                    <th>Nem (%)</th>
                    <th>Rüzgar Hızı (km/h)</th>
                    <th>Durum</th>
                </tr>
                </thead>
                <tbody>
                <tr>
                    <td>{{ weatherData.city }}</td>
                    <td>{{ weatherData.temperature }}</td>
                    <td>{{ weatherData.humidity }}</td>
                    <td>{{ weatherData.windSpeed }}</td>
                    <td>{{ weatherData.condition }}</td>
                </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>
<script>
export default {
    name: 'WeatherApp',
    data() {
        return {
            countries: ['Türkiye', 'ABD', 'Almanya'],
            cities: [],
            selectedCountry: '',
            selectedCity: '',
            weatherData: {
                weatherList: {
                    id: '',
                    main: '',
                    description: '',
                    icon: ''
                },
                main: {
                    temp: '',
                    feels_like: '',
                    pressure: '',
                    humidity: ''
                },
                sys: {
                    country:''
                },
                wind: {
                    speed: '',
                },
                timezone: '',
                name: '',
                dt: '',
            },
            languageTr: {
                header: 'Canlı Hava Durumu',
                button: 'Hava Durumunu Getir',
                weather: {
                    temperature: 'Sıcaklık(C)',
                    wind: 'Rüzgar(km/h)',
                    state: 'Durum',
                    City: 'Şehir',
                    Country: 'Ülke',
                    Time: 'Zaman',
                    feelsLike: 'Hissedilen',
                }
            }

        };
    },
    methods: {
        fetchCities() {
            if (this.selectedCountry === 'Türkiye') {
                this.cities = ['Ankara', 'İstanbul', 'İzmir'];
            } else if (this.selectedCountry === 'ABD') {
                this.cities = ['New York', 'Los Angeles', 'Chicago'];
            } else if (this.selectedCountry === 'Almanya') {
                this.cities = ['Berlin', 'Münih', 'Hamburg'];
            }
            this.selectedCity = '';
        },
        fetchWeatherData() {
            // Hava durumu verilerini API'dan çek
            this.weatherData = {
                city: this.selectedCity,
                temperature: 25,
                humidity: 60,
                windSpeed: 15,
                condition: 'Güneşli'
            };
        }
    }
};
</script>
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

.weather-app {
    width: 100%;
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: 'Roboto', sans-serif;
    overflow: hidden;
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
    opacity: 0.8;
    z-index: -1;
}

.header {
    width: 100%;
    max-width: 1200px;
    padding: 40px;
    background: #007bff;
    color: #fff;
    text-align: center;
    border-bottom-left-radius: 30px;
    border-bottom-right-radius: 30px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
    margin-bottom: 40px;
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
    max-width: 800px;
    background: #fff;
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
    margin-top: 20px;
}

.weather-table-container h2 {
    font-size: 2em;
    margin-bottom: 20px;
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
</style>
