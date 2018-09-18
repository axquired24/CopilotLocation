<template>
  <div class="form-group">
    <label>{{ title }}</label>
    <select class="form-control" v-model="selected">
      <option v-for="opt in opts" :value="opt">{{ opt.label }}</option>
    </select>
  </div>
</template>

<script>
  import {DEFAULT_OPTION} from '@/helper/general'
  const axios = require('axios')
  // const _ = require('lodash')

  export default {
    name: 'BaseSelect',
    data: () => {
      return {
        opts: DEFAULT_OPTION,
        selected: DEFAULT_OPTION[0]
      }
    },
    props: {
      title: {
        type: String,
        default: "Select Box"
      }
    },
    methods: {
      cobaLagi: (self) => {
        console.log(this)
        console.log(self)
        // console.log(self.opts)
        // console.log(self.selected)
      },
      loadData: (self, apiUrl, params) => {
        axios.get(apiUrl, {
          params: params
        })
        .then((resp) => {
          let opts = self.toLabelVal(resp.data)

          self.opts = opts
          self.selected = opts[0]
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
    }
  }
</script>