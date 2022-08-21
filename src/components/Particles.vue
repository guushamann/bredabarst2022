<template>
  <Canvas
    global-composite-operation="source-over"
    v-on:canvasInited="canvasInited"
    v-on:canvasResize="canvasResize"
    v-on:canvasMouseMove="canvasMouseMove"
  />
</template>

<script>
import Canvas from "./Canvas";
import Proton from "proton-engine";
import RAFManager from "raf-manager";
import image from "../assets/image.js";

export default {
  name: "App",
  components: {
    Canvas,
  },
  methods: {
    canvasInited: function (canvas) {
      this.createProton(canvas);
    },
    canvasResize: function ({ width, height }) {
      this.renderer && this.renderer.resize(width, height);
    },

    canvasMouseMove(e) {
      if (e.layerX || e.layerX === 0) {
        this.emitter.p.x = e.layerX;
        this.emitter.p.y = e.layerY;
      } else if (e.offsetX || e.offsetX === 0) {
        this.emitter.p.x = e.offsetX;
        this.emitter.p.y = e.offsetY;
      }
    },

    createProton(canvas) {
      const proton = new Proton();
      const emitter = new Proton.Emitter();
      emitter.rate = new Proton.Rate(new Proton.Span(1, 3), 0.02);

      emitter.addInitialize(new Proton.Body(image, 28.6, 36.4));
      emitter.addInitialize(new Proton.Mass(0.5));
      emitter.addInitialize(new Proton.Life(1.5, 2.2));
      emitter.addInitialize(
        new Proton.Velocity(5, Proton.getSpan(0, 360), "polar")
      );

      emitter.addBehaviour(new Proton.Rotate());
      emitter.addBehaviour(new Proton.Gravity(3));
      emitter.addBehaviour(new Proton.Alpha(0.6, 1));
      emitter.addBehaviour(
        new Proton.CrossZone(
          new Proton.RectZone(0, 0, canvas.width, canvas.height),
          "bound"
        )
      );

      emitter.p.x = canvas.width / 2;
      emitter.p.y = canvas.height / 2;
      proton.addEmitter(emitter);
      emitter.emit();

      const renderer = new Proton.CanvasRenderer(canvas);
      const context = canvas.getContext("2d");
      renderer.onProtonUpdate = () => {
        context.fillStyle = "rgba(0, 0, 0, 0.08)";
        context.fillRect(0, 0, canvas.width, canvas.height);
      };
      proton.addRenderer(renderer);

      this.proton = proton;
      this.emitter = emitter;
    },
    renderProton: function () {
      if (this.proton) {
        this.proton.update();
        this.proton.stats.update(2);
      }
    },
  },
  created() {
    RAFManager.add(this.renderProton);
  },
  beforeDestory() {
    try {
      this.proton.destroy();
      RAFManager.remove(this.renderProton);
    } catch (e) {}
  },
};
</script>
