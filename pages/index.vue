<template>
  <v-app>
    <app-bar></app-bar>
    <v-row class="margin-layout">
      <v-col> <MacaronEditor /></v-col
    ></v-row>
  </v-app>
</template>
<script>
import AppBar from '~/components/AppBar.vue'
export default {
  components: {
    AppBar,
  },
}
</script>
<style scoped>
.margin-layout {
  margin-top: 15%;
}
</style>
