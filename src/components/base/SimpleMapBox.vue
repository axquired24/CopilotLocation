<template>
  <div class="box box-default">
    <l-map style="min-height: 50vh" :zoom="zoom" :center="center" ref="leafletMap">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <LGeoJson :geojson="geojson.data" :options="geojson.options" :visible="geojson.visible" ref="leafletGeojson"></LGeoJson>
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
            style: (feature) => { return this.polygonStyle(feature, this) },
            onEachFeature: (feature, layer) => {return this.onEachFeature(feature, layer, this)}
          },
          visible: false
        }
      }
    },
    mounted() {
      this.loadGeoJson(this, usStatesUrl)
      this.loadLegend(this)
    },
    methods: {
      loadLegend: (self) => {
        let mapObject = self.$refs.leafletMap.mapObject
        let legend = L.control({position: 'bottomright'})

        legend.onAdd = (map) => {
          var div = L.DomUtil.create('div', 'leaflet-info leaflet-legend'),
            grades = [0, 10, 20, 50, 100, 200, 500, 1000],
            labels = [],
            from, to;

          for (var i = 0; i < grades.length; i++) {
            from = grades[i]
            to = grades[i + 1]

            labels.push(
              '<i style="background:' + self.getColor(from + 1) + '"></i> ' +
              from + (to ? '&ndash;' + to : '+'))
          }

          div.innerHTML = labels.join('<br>')
          return div
        }
        legend.addTo(mapObject)
        return true;
      },
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
      resetHighlight: (e, self) => {
        let geojson = self.$refs.leafletGeojson.mapObject
        geojson.resetStyle(e.target)
		    //info.update()
      },
      highlightFeature: (e, self) => {
        let layer = e.target;
        layer.setStyle({
          weight: 5,
          color: '#666',
          dashArray: '',
          fillOpacity: 0.7
        });

        if (!L.Browser.ie && !L.Browser.opera && !L.Browser.edge) {
          layer.bringToFront();
        }
        // info.update(layer.feature.properties)
      },
      showTooltip: (layer, self) => {
        let mapObject = self.$refs.leafletMap.mapObject
        let properties = layer.feature.properties
        let textProps = '<div class="tooltip-leaflet"> <h3>Details</h3> <span>Location Name: '+properties.name+'</span> <small>Density: '+properties.density+'</small> </div>'
        layer.bindTooltip(textProps, { sticky: true, interactive: true })
      },
      onEachFeature: (feature, layer, self) => {
        // bind tooltip on
        self.showTooltip(layer, self)

        layer.on({
          mouseover: (e) => {
              self.highlightFeature(e, self)
            },
          mouseout: (e) => { return self.resetHighlight(e, self) }
          // click: zoomToFeature
        });
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

<style lang='less'>
/* #map { width: 800px; height: 500px; } */
.leaflet-info { padding: 6px 8px; font: 14px/16px Arial, Helvetica, sans-serif; background: white; background: rgba(255,255,255,0.8); box-shadow: 0 0 15px rgba(0,0,0,0.2); border-radius: 5px; } .info h4 { margin: 0 0 5px; color: #777; }
.leaflet-legend { text-align: left; line-height: 18px; color: #555; } 
.leaflet-legend i { width: 18px; height: 18px; float: left; margin-right: 8px; opacity: 0.7; }

.tooltip-leaflet {
  min-width: 200px;

  h3 {
    color: darkblue;
    margin-top:0;
    padding-bottom: 10px;
    border-bottom: #CCC 1px solid;
  }

  span, small {
    font-size: 14px;
    display: block;
  }
}
</style>