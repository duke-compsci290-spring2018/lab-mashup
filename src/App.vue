<template>
<div>
    <h1>Recent Earthquakes Plotted</h1>
    <gmap-map
        :center="center"
        :zoom="2"
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
        >
        </gmap-marker>
        <gmap-circle
            v-for="(c, index) in circles"
            :key="c.id"
            :center="c.center"
            :radius="c.radius"
            :options="c.options"
            @mouseover="infoText = c.text"
            @mouseout="infoText = ''"
        ></gmap-circle>
        <div slot="visible">
            <div style="bottom: 0; left: 0; background-color: blue; color: white; position: absolute; z-index: 100">
              {{infoText}}
            </div>
        </div>
    </gmap-map>
</div>
</template>

<script>
var USGS_API_URL = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/2.5_week.geojson'
var circleOptions = {
    strokeColor: '#FF0000',
    strokeOpacity: 0.8,
    strokeWeight: 1,
    fillColor: '#FF0000',
    fillOpacity: 0.3
}

export default {
    name: 'App',
    data () {
        return {
            infoText: '',
            center: {
                lat: 40,
                lng: -35
            },
            markers: [
                {
                    position: { lat: 35.99, lng: -78.89 } // Durham, NC
                }
            ],
            circles: []
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
                            data.features.forEach(e => {
                                this.circles.push({
                                    id: e.id,
                                    center: {
                                        lng: e.geometry.coordinates[0],
                                        lat: e.geometry.coordinates[1]
                                    },
                                    radius: e.properties.mag * 60000,
                                    text: e.properties.title,
                                    options: circleOptions
                                })
                            })
                            console.log(this.circles.length)
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
