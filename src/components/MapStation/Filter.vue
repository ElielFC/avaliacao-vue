<template>
  <v-row>
    <v-col>
      <v-row>
        <v-col>
					Filtros
          <v-select
            v-model="filter.station_type"
            :items="computedStationType"
            :menu-props="{ maxHeight: '400' }"
            label="Todas"
            multiple
            chips
            hint="Selecione os Tipos de Estação"
            persistent-hint
          ></v-select>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-select
            v-model="filter.station_id"
            :items="computedStation"
            :menu-props="{ maxHeight: '400' }"
            label="Todas"
            multiple
            chips
            hint="Selecione as Estações"
            persistent-hint
          ></v-select>
        </v-col>
      </v-row>
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: {
    station: {
      type: Array,
      required: true,
    },
    stationType: {
      type: Array,
      required: true,
    },
    filter: {
      type: Object,
      default: () => {},
    },
  },
  computed: {
    computedStationType() {
      return this.stationType.map((item) => {
        return {
          value: item.id,
          text: item.name,
        };
      });
    },
    computedStation() {
      return this.station
        .filter((item) => {
          if (this.filter.station_type.length > 0) {
            return this.filter.station_type.indexOf(item.station_type_id) > -1;
          }
          return true;
        })
        .map((item) => {
          return {
            value: item.id,
            text: item.name,
          };
        });
    },
  },
};
</script>