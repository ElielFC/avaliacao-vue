<template>
  <div>
    <div ref="map" style="width: 100%; height: 600px"></div>
  </div>
</template>

<script>
import { Style, Icon } from "ol/style";
import { Feature, Map, View, Overlay } from "ol/index";
import { OSM, Vector as VectorSource } from "ol/source";
import { Point } from "ol/geom";
import { Tile as TileLayer, Vector as VectorLayer } from "ol/layer";
import "ol/ol.css";
import { useGeographic } from "ol/proj";
import { format } from 'date-fns';
export default {
  props: {
    source: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      map: null,
    };
  },
  watch: {
    source: {
      handler() {
        this.updateLayer();
      },
      deep: true,
    },
  },
  mounted() {
    useGeographic();
    this.mountMap();
  },
  methods: {
    mountMap() {
      this.map = new Map({
        target: this.$refs.map,
        view: new View({
          center: [-50.73, -24.57],
          zoom: 7,
        }),
        layers: [
          new TileLayer({
            source: new OSM(),
          }),
          new VectorLayer({
            source: new VectorSource({
              features: this.getFeatures(),
            }),
          }),
        ],
      });

      this.map.addOverlay(new Overlay({
        element: this.$refs.popup,
        stopEvent: false
      }));

      this.map.on("click", (event) => {
        this.showPopup(event.pixel);
      });
    },
    showPopup(pixel) {
      const features = this.map.getFeaturesAtPixel(pixel);
      if (features.length > 0) {
        const station = features[0].get("station");
        this.$swal.fire({
          type: "info",
          width: '45rem',
          html: `
            <h3>#${station.id} ${station.name}</h3>
            <p>Latitude: ${station.latitude} Longitude: ${station.longitude}</p>
            <p>Elevação: ${station.elevation_meters}m</p>
            <p>Inicio de Operação: ${format(new Date(station.operation_start_date), 'dd-MM-yyyy')} Fim de Operação: ${format(new Date(station.operation_end_date), 'dd-MM-yyyy')}</p>
            <p>Tipo de Estação: ${station.station_type_name}</p>
          `,
        })
      }
    },
    getFeatures() {
      return this.source.map((station) => {
        const feature = new Feature(
          new Point([station.longitude, station.latitude])
        );
        feature.setStyle(
          new Style({
            image: new Icon({
              anchor: [0.5, 0.96],
              src: "ico.svg",
              color: station.color,
            }),
          })
        );

        feature.set('station', station);

        return feature;
      });
    },
    updateLayer() {
      if (!this.map) {
        return;
      }

      const features = this.getFeatures();

      this.map.getLayers().forEach((layer) => {
        if (layer instanceof VectorLayer) {
          layer.setSource(
            new VectorSource({
              features,
            })
          );
        }
      });
    },
  },
};
</script>