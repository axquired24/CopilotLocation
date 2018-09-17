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

  export default {
    name: 'BaseSelect',
    data: () => {
      return {
      }
    },
    props: {
      title: {
        type: String,
        default: "Select Box"
      },
      opts: {
        default: () => DEFAULT_OPTION
      },
      selected: {
        default: () => DEFAULT_OPTION[0]
      }
    }
  }
</script>