<template>
  <v-app>
    <v-content>
      <devicelist/>
    </v-content>
  </v-app>
</template>

<script>
import devicelist from './components/devicelist'

export default {
  name: 'App',
  components: {
    devicelist
  },
  data: () => ({
  }),
  methods: {
  }
}
</script>

<style>
</style>
