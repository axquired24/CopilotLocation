<template>
  <div class="box box-default">
    <l-map style="min-height: 50vh" :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <LGeoJson :geojson="geojson.data" :options="geojson.options" :visible="geojson.visible"></LGeoJson>
    </l-map>
  </div>
</template>

<script>
  import "leaflet/dist/leaflet.css"
  import { LMap, LTileLayer, LMarker, LGeoJson } from 'vue2-leaflet';

  const axios = require('axios')
  const usStatesUrl = "https://api.myjson.com/bins/qv384"
  // const usStatesUrl = "https://rawgit.com/gregoiredavid/france-geojson/master/regions/pays-de-la-loire/communes-pays-de-la-loire.geojson"

  export default {
    name: 'SimpleMapBox',
    components: {
      LMap,
      LTileLayer,
      LMarker,
      LGeoJson
    },
    data () {
      return {
        zoom:4,
        center: L.latLng(37.8, -96),
        url:'https://api.tiles.mapbox.com/v4/mapbox.light/{z}/{x}/{y}.png?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw',
        attribution:'&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
        geojson: {
          data: null,
          options: {
            style: (feature) => { return this.polygonStyle(feature, this) }
          },
          visible: false
        }
      }
    },
    mounted() {
      this.loadGeoJson(this, usStatesUrl)
    },
    methods: {
      loadGeoJson: (self, dataUrl) => {
        axios.get(dataUrl)
          .then((resp) => {
            let geojson = resp.data
            self.geojson.data = geojson
            self.geojson.visible = true
          })
          .catch((err) => {
            console.log(err)
          })
      },
      getColor: (d) => {
        return d > 1000 ? '#800026' :
                d > 500  ? '#BD0026' :
                d > 200  ? '#E31A1C' :
                d > 100  ? '#FC4E2A' :
                d > 50   ? '#FD8D3C' :
                d > 20   ? '#FEB24C' :
                d > 10   ? '#FED976' :
                      '#FFEDA0'
      },
      polygonStyle: (feature, self) => {
        let colorCode = self.getColor(feature.properties.density)
        return {
          weight: 2,
          opacity: 1,
          color: 'white',
          dashArray: '3',
          fillOpacity: 0.7,
          fillColor: colorCode
        }
      }
    }
  }
</script>

