<template>
  <v-container>
    <v-row class="text-center">
      <v-col :span="12" style="background-image: url(https://jointecnologia.com.br/wp-content/themes/theme-bones-master/library/images/bg-top.jpg);background-size: cover;">
         <h1 class="text-h1 white--text pt-16 pb-16">Join Tecnologia</h1>
      </v-col>
    </v-row>
    <v-row>
      <v-col class="col-9 pl-0">
        <Map :source="source"/>
      </v-col>
      <v-col class="col-3 pr-0">
        <VFilter :station="station" :station-type="station_type" :filter.sync="filter"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Map from '../MapStation/Map.vue';
import VFilter from '../MapStation/Filter.vue';
const STATION = require('../../data/station.json');
const STATION_TYPE = require('../../data/station_type.json');
export default {
  components: {
    Map,
    VFilter
  },
  data() {
    return {
      station: STATION,
      station_type: STATION_TYPE,
      filter: {
        station_type: [],
        station_id: []
      }
    }
  },
  computed: {
    source() {
      let source = STATION;
      if (this.filter.station_type.length > 0) {
        source = source.filter(item => {
          return this.filter.station_type.indexOf(item.station_type_id) > -1;
        });
      }
      if (this.filter.station_id.length > 0) {
        source = source.filter(item => {
          return this.filter.station_id.indexOf(item.id) > -1;
        });
      }

      return source.map(item => {
        return {
          ...item,
          color: STATION_TYPE.find(({id}) => id === item.station_type_id)?.color || '#ffffff',
          station_type_name: STATION_TYPE.find(({id}) => id === item.station_type_id)?.name || ''
        }
      });
    }
  }
};
</script>
