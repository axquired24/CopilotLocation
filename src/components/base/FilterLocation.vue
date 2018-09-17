<template>
  <div class="box box-warning">
      <div class="box-header">
          <h3 class="box-title">Location</h3>
      </div>
      <div class="box-body">
          <BaseSelect title="State" :opts="stateLocation.opts" :selected="stateLocation.selected" ref="StateFilter"></BaseSelect>
          <BaseSelect title="Region" :opts="region.opts" :selected="region.selected" ref="RegionFilter"></BaseSelect>
          <BaseSelect title="City" :opts="city.opts" :selected="city.selected" ref="CityFilter"></BaseSelect>
      </div>
  </div>
</template>

<script>
  import BaseSelect from '@/components/base/BaseSelect.vue'
  import {DEFAULT_OPTION} from '@/helper/general'

  const axios = require('axios')
  const _ = require('lodash')
  const basepath = 'https://api.mockaroo.com/api/42f8ee20?count=10&key=6c398fb0'
  const defaultLocation = {val: '', label: 'Select parent location...'}

  export default {
    name: 'FilterLocation',
    data: () => {
      return {
        stateLocation: {
          opts: DEFAULT_OPTION,
          selected: DEFAULT_OPTION[0]
        },
        region: {
          opts: [defaultLocation],
          selected: defaultLocation
        },
        city: {
          opts: [defaultLocation],
          selected: defaultLocation
        }
      }
    },
    components: {
      BaseSelect
    },
    mounted() {
      this.loadState(this)
    },
    methods: {
      loadState: (self) => {
        axios.get(basepath, {
          params: {}
        })
        .then((resp) => {
          let opts = self.toLabelVal(resp.data)

          self.stateLocation.opts = opts
          self.stateLocation.selected = opts[0]
        })
        .catch((err) => {
          console.log(err)
        })
      },

      loadRegion: (self) => {
        axios.get(basepath, {
          params: {
            state: self.stateLocation.selected.val
            }
        })
        .then((resp) => {
          let opts = self.toLabelVal(resp.data)

          self.region.opts = opts
          self.region.selected = opts[0]
        })
        .catch((err) => {
          console.log(err)
        })
      },

      loadCity: (self) => {
        axios.get(basepath, {
          params: {
            state: self.stateLocation.selected.val,
            region: self.region.selected.val,
            }
        })
        .then((resp) => {
          let opts = self.toLabelVal(resp.data)

          self.city.opts = opts
          self.city.selected = opts[0]
        })
        .catch((err) => {
          console.log(err)
        })
      },

      toLabelVal: function(idNameFormat) {
        let ret = _.map(idNameFormat, (v) => {
          return {val: v.id, label: v.name}
        })
        return ret
      }
    } // / methods
  }
</script>