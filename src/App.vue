<template>
    <gmap-map
        :center="center"
        :zoom="3"
        @click="addMarker"
        style="height: 480px;"
    >
        <gmap-marker
            v-for="(m, index) in markers"
            :key="index"
            :position="m.position"
            :clickable="true"
            :draggable="true"
            @click="center = m.position"
        ></gmap-marker>
        <gmap-circle
            v-for="(c, index) in circles"
            :key="c.radius"
            :center="c.center"
            :radius="c.radius"
            :options="c.options"
        ></gmap-circle>
    </gmap-map>
</template>

<script>
var USGS_API_URL = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/2.5_week.geojson'

export default {
    name: 'App',
    data () {
        return {
            center: {
                lat: 40,
                lng: -35
            },
            markers: [
                {
                    position: { lat: 35.99, lng: -78.89}, // Durham, NC
                },
                {
                    position: { lat: 48.85, lng: 2.35},   // Paris, France
                }
            ],
            circles: [
                {
                    center: { lat: 35.99, lng: -78.89},  // Durham, NC
                    radius: 100000,
                    options: {
                        strokeColor: '#FF0000',
                        strokeOpacity: 0.8,
                        strokeWeight: 1,
                        fillColor: '#FF0000',
                        fillOpacity: 0.3
                    }
                },
                {
                    center: { lat: 40, lng: -35},   // Paris, France
                    radius: 500000
                }
            ]
        }
    },
    methods: {
        addMarker (event) {
            this.markers.push({
                position: { lat: event.latLng.lat(), lng: event.latLng.lng() }
            })
        },
        getEarthquakeData (url) {
            fetch(url).then(response => response.json())
                      .then(data => {
                            // TODO: create circles to represent significant recent earthquakes
                            console.log(data)
                       })
                      .catch(error => console.log(error))
        }
    },
    mounted () {
        this.getEarthquakeData(USGS_API_URL)
    }
}
</script>

<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
