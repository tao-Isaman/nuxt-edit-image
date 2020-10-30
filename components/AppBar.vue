<template>
  <v-app-bar color="primary" dense app>
    <v-spacer></v-spacer>
    <v-toolbar-title color="primary">คณะราษฎร</v-toolbar-title>

    <v-spacer></v-spacer>
  </v-app-bar>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js',
    }
  },
}
</script>

<style lang="scss" scoped></style>
