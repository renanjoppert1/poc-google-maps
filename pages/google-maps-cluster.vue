<template>
  <div>
    <div style="display: flex; width: 100%">
      <input type="text" v-model="cep" placeholder="Digite o CEP" />
      <input
        type="text"
        v-model="cepLocations"
        placeholder="Digite os CEPs que deseja carregar separados por vírgulas"
      />
      <input type="button" value="Buscar" @click="buscar" />
    </div>
    <div id="map"></div>
  </div>
</template>

<script>
import { MarkerClusterer } from '@googlemaps/markerclusterer'
import { Loader } from '@googlemaps/js-api-loader'

let map

export default {
  data() {
    return {
      cep: '80310370',
      cepLocations: '80310370,80420011',
      locations: [
        { lat: -31.56391, lng: 147.154312 },
        { lat: -33.718234, lng: 150.363181 },
        { lat: -33.727111, lng: 150.371124 },
        { lat: -33.848588, lng: 151.209834 },
        { lat: -33.851702, lng: 151.216968 },
        { lat: -34.671264, lng: 150.863657 },
        { lat: -35.304724, lng: 148.662905 },
        { lat: -36.817685, lng: 175.699196 },
        { lat: -36.828611, lng: 175.790222 },
        { lat: -37.75, lng: 145.116667 },
        { lat: -37.759859, lng: 145.128708 },
        { lat: -37.765015, lng: 145.133858 },
        { lat: -37.770104, lng: 145.143299 },
        { lat: -37.7737, lng: 145.145187 },
        { lat: -37.774785, lng: 145.137978 },
        { lat: -37.819616, lng: 144.968119 },
        { lat: -38.330766, lng: 144.695692 },
        { lat: -39.927193, lng: 175.053218 },
        { lat: -41.330162, lng: 174.865694 },
        { lat: -42.734358, lng: 147.439506 },
        { lat: -42.734358, lng: 147.501315 },
        { lat: -42.735258, lng: 147.438 },
        { lat: -43.999792, lng: 170.463352 },
      ],
      mapCenter: { lat: -28.024, lng: 140.887 }
    }
  },
  mounted() {
    console.clear()
    this.loadGoogleMaps()
  },
  methods: {
    buscar() {
      const ceps = this.cepLocations.split(',')
      this.geocoding(ceps)
    },
    loadGoogleMaps(mapCenter, zoom) {
      const loader = new Loader({
        apiKey: '',
        version: 'weekly',
        // ...additionalOptions,
      })

      if( mapCenter === undefined ){
        mapCenter = { lat: -28.024, lng: 140.887 }
      }

      if( zoom === undefined ){
        zoom = 3
      }

      loader.load().then(() => {
        map = new window.google.maps.Map(document.getElementById('map'), {
          // eslint-disable-next-line object-shorthand
          zoom: zoom,
          center: mapCenter,
        })
        // Create an array of alphabetical characters used to label the markers.
        const labels = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
        // Add some markers to the map.
        const markers = this.locations.map((location, i) => {
          return new window.google.maps.Marker({
            position: location,
            label: labels[i % labels.length],
          })
        })

        // Add a marker clusterer to manage the markers.
        // eslint-disable-next-line no-unused-vars
        const makerClusterer = new MarkerClusterer({ markers, map })
      })
    },
    async getLatLong(cep) {
      const geocoder = new window.google.maps.Geocoder()
      const data = await geocoder.geocode(
        {
          address: cep,
        },
        function (results, status) {
          return results[0].geometry.location
        }
      )

      // return data
      return data.results[0]?.geometry?.location
    },
    async geocoding(markers) {
      // eslint-disable-next-line
      let places = []
      try {
        for (let i = 0; i < markers.length; i++) {
          const el = markers[i];
          places.push(await this.getLatLong(el))
        }

        this.locations = places
        this.loadGoogleMaps(places[0], 10)
      } catch (error) {}

    },
  },
}
</script>

<style>
#map {
  height: 500px;
}
</style>
