<template>
  <v-app>

    <v-main>
      <v-card class="background">
      <v-container>
        <Nuxt />
      </v-container>
      </v-card>
    </v-main>

  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
/*      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Inspire',
          to: '/inspire'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'*/
    }
  }
}
</script>

<style>
.background{
  background-color: white !important;
  width: 100%;
  height: 100%;
}
</style>
