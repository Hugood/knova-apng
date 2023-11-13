<template>
  <v-stage ref="stage" :config="stageSize">
    <v-layer ref="layer">
      <!-- apng -->
      <v-image :config="{ image: src, x: 0, y: 0 }" />
    </v-layer>
  </v-stage>
</template>

<script>
import parseAPNG from "apng-js";
import Konva from "konva";
import elephant from "./assets/elephant.png";
// image source:  https://apng.onevcat.com/demo/
// If this image cannot be used, please leave a message to let me know and I will replace it immediately.

export default {
  components: {},
  data() {
    return {
      stageSize: {
        width: 500,
        height: 400,
        x: 0,
        y: 0,
        centeredScaling: true,
        draggable: true,
      },
      src: null,
      animation: null,
    };
  },
  mounted() {
    // get apng buffer
    function getImgBuffer(url) {
      return new Promise(async (resolve) => {
        const blob = await fetch(url).then((res) => res.blob());
        const reader = new FileReader();
        reader.readAsArrayBuffer(blob);
        reader.onload = () => {
          resolve(reader.result);
        };
      });
    }

    // import apng to Konva v-image canvas
    this.$nextTick(() => {
      const canvas = document.createElement("canvas");
      canvas.width = 500;
      canvas.height = 400;
      const ctx = canvas.getContext("2d");
      const layer = this.$refs.layer.getNode();

      getImgBuffer(elephant).then((res) => {
        let apng = parseAPNG(res);
        apng.getPlayer(ctx).then((player) => {
          player.play();
        });

        this.src = canvas;
        this.animation = new Konva.Animation(() => {}, layer);
        this.animation.start();
      });
    });
  },
};
</script>
