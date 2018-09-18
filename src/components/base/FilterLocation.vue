<template>
  <div class="box box-warning">
      <div class="box-header">
          <h3 class="box-title">Location</h3>
      </div>
      <div class="box-body">
          <BaseSelect title="State" ref="StateFilter"></BaseSelect>
          <BaseSelect title="Region" ref="RegionFilter"></BaseSelect>
          <BaseSelect title="City" ref="CityFilter"></BaseSelect>
      </div>
  </div>
</template>

<script>
  import BaseSelect from '@/components/base/BaseSelect.vue'
  import {DEFAULT_OPTION} from '@/helper/general'

  const axios = require('axios')
  const _ = require('lodash')
  // const basepath = 'https://api.mockaroo.com/api/42f8ee20?count=10&key=6c398fb0'
  const basepath = 'https://api.myjson.com/bins/16izaw'
  const defaultOptionObj = {
    opt: DEFAULT_OPTION,
    selected: DEFAULT_OPTION[0]
  }

  export default {
    name: 'FilterLocation',
    components: {
      BaseSelect
    },
    data: () => {
      return {
        stateLocation: defaultOptionObj,
        region: defaultOptionObj,
        city: defaultOptionObj
      }
    },
    mounted() {
      this.stateLocation = this.$refs.StateFilter
      this.region = this.$refs.RegionFilter
      this.city = this.$refs.CityFilter
      this.loadStateLocation(this)
    },
    computed: {
        stateLocationVal() {
          return this.stateLocation.selected
        },
        regionVal() {
            return this.region.selected
        },
        cityVal() {
            return this.city.selected
        }
    },
    watch: {
      stateLocationVal() {
        console.log("State Location Changed")
        this.loadRegion(this)
      },
      regionVal() {
        console.log("region Changed")
        this.loadCity(this)
      },
      cityVal() {
        console.log("city Location Changed")
      }
    },
    methods: {
      loadStateLocation: (self) => {
        self.stateLocation.loadData(self.stateLocation, basepath, {})
      },
      loadRegion: (self) => {
        self.region.loadData(self.region, basepath, {
          stateLocation: self.stateLocation.selected.val
        })
      },
      loadCity: (self) => {
        self.city.loadData(self.city, basepath, {
          stateLocation: self.stateLocation.selected.val,
          region: self.region.selected.val
        })
      }
    } // / methods
  }
</script>