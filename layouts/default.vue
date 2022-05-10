<template>
  <v-app>
    <v-app-bar
      fixed
      app
      class="d-flex justify-center text-h4 font-weight-black"
      dark
      color="deep-orange accent-2"
      flat
    >
      <div class="white--text text-h5">
        会議開くくん
      </div>
    </v-app-bar>
    <v-main class="grey lighten-4">
      <div class="mx-auto white" style="max-width: 450px;">
         <Nuxt />
      </div>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      fixed: false,
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  }
}
</script>
