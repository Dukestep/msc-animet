<template>
  <v-tooltip class="temporal-tooltip" bottom v-if="item.get('layerIsTemporal')">
    <template v-slot:activator="{ on, attrs }">
      <v-btn
        x-large
        v-bind="attrs"
        v-on="on"
        icon
        :color="color"
        :disabled="isAnimating"
        @click="snapLayerToAnimation(item)"
      >
        <v-icon>
          {{ color !== "" ? "mdi-clock-check" : "mdi-clock" }}
        </v-icon>
      </v-btn>
    </template>
    <v-container class="primary darken-2 rounded pl-4 pr-4" v-if="color !== ''">
      <v-row>
        {{ $t("SnappedLayer") }}
      </v-row>
    </v-container>
    <v-container v-else>
      <v-row>
        {{ $t("SnapLayerToExtent") }}
      </v-row>
    </v-container>
    <v-container>
      <v-row
        v-if="
          !(item.get('layerDateIndex') < 0) && item.get('layerVisibilityOn')
        "
      >
        {{ $t("LayerBarCurrentTooltip") }} :
        {{
          localeDateFormat(
            item.get("layerDateArray")[item.get("layerDateIndex")],
            item.get("layerTimeStep")
          )
        }}
      </v-row>
      <v-row>
        {{ $t("LayerBarStartsTooltip") }} :
        {{
          localeDateFormat(
            item.get("layerStartTime"),
            item.get("layerTimeStep")
          )
        }}
      </v-row>
      <v-row>
        {{ $t("LayerBarEndsTooltip") }} :
        {{
          localeDateFormat(item.get("layerEndTime"), item.get("layerTimeStep"))
        }}
      </v-row>
      <v-row>
        {{ $t("LayerBarStepTooltip") }} :
        {{ item.get("layerTimeStep") }}
      </v-row>
    </v-container>
  </v-tooltip>
  <v-btn x-large icon disabled v-else>
    <v-icon>
      {{ "mdi-clock-remove" }}
    </v-icon>
  </v-btn>
</template>

<script>
import { mapGetters, mapState } from "vuex";

import datetimeManipulations from "../../mixins/datetimeManipulations";

export default {
  mixins: [datetimeManipulations],
  props: ["item", "color"],
  methods: {
    snapLayerToAnimation(layer) {
      if (layer.get("layerName") !== this.getMapTimeSettings.SnappedLayer) {
        if (this.getMapTimeSettings.Step === layer.get("layerTimeStep")) {
          this.$store.dispatch(
            "Layers/setMapSnappedLayer",
            layer.get("layerName")
          );
        } else {
          this.changeMapTime(layer.get("layerTimeStep"), layer);
        }
      }
    },
  },
  computed: {
    ...mapGetters("Layers", ["getMapTimeSettings"]),
    ...mapState("Layers", ["isAnimating"]),
  },
};
</script>