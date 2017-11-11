<template>
    <div v-if="loading" >
        <h4>Loading...</h4>
        <icon name="spinner" scale="4" spin></icon>
    </div>
    <div v-else>
        <h1 v-model="location">Local Weather in {{ location.city }}, {{ location.country }}</h1>
        <h2 v-if="temp_toggle" v-model="temp_c">{{ temp_c }}&deg <span class="temp" @click="toggleTemp">C</span></h2>
        <h2 v-else v-model="temp_f">{{ temp_f }}&deg <span class="temp" @click="toggleTemp">F</span></h2>
        <h3>Conditions: <span v-for="item in conditions">{{ item }},</span></h3>
        <div>
            <img class="icon" v-for="icon in icons" v-bind:src="icon"/>
        </div>
    </div>
</template>

<script>
import Icon from 'vue-awesome/components/Icon'
import 'vue-awesome/icons/spinner'
export default {
    name: 'Weather',
    components: {
        Icon
    },
    data() {
        return {
            loading: true,
            location: {
                city: "City",
                country: "Country",
            },
            temp_c: null,
            temp_f: null,
            temp_toggle: 0,
            conditions: [],
            icons: []
        }
    },
    mounted: function() {

        const vm = this

        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(locationInfo);
        }

        function locationInfo(position) {
            const lat = position.coords.latitude;
            const lon = position.coords.longitude;

            console.log(lon, lat)

            vm.$http.get('https://fcc-weather-api.glitch.me/api/current?lat='+lat+'&lon='+lon)
                .then(function(response) {
                    console.log(response);
                    vm.location.city = response.data.name;
                    vm.location.country = response.data.sys.country;
                    vm.temp_c = response.data.main.temp;
                    vm.temp_f = (response.data.main.temp * (9/5)) + 32;
                    vm.weather_description = response.data.weather[0].description;
                    console.log(response.data.weather);
                    for(var i in response.data.weather) {
                        console.log(response.data.weather[i].description);
                        console.log(response.data.weather[i].icon);
                        vm.conditions.push(response.data.weather[i].description);
                        vm.icons.push(String(response.data.weather[i].icon));
                    vm.loading = false;
                    }
                })
                .catch(function(error) {
                    console.log(error);
                })

        }

    },
    methods: {
        toggleTemp() {
            const vm = this;

            if(vm.temp_toggle == 0) {
                vm.temp_toggle = 1
            } else {
                vm.temp_toggle = 0
            }
        }
    }
}
</script>

<style>
body {
    background-color: #161616;
}

h1, h2, h3, h4 {
    color: white;
}

.temp {
    color: #0080ff;
    cursor: pointer;
}

.icon {
    height: 75px;
}

</style>