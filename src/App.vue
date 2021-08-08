<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        Avaliação Vue Js
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <MapStation/>
    </v-main>
  </v-app>
</template>

<script>
import MapStation from './components/MapStation';

export default {
  name: 'App',

  components: {
    MapStation,
  },

  data: () => ({
    //
  }),
};
</script>
