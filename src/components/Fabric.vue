<template>
  <div>
    <canvas ref="canvas" height="600" width="400"></canvas>
    <button
      @click="test('https://images.unsplash.com/photo-1578847298326-10909089ccdb?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1275&q=80')"
    >test</button>
  </div>
</template>

<script>
import { fabric } from "fabric";
export default {
  data() {
    return {
      canvas: null
    };
  },
  mounted() {
    this.canvas = new fabric.Canvas(this.$refs.canvas);
  },
  methods: {
    test(url) {
      fabric.Image.fromURL(
        url,
        img => {
          img.scaleToWidth(400);
          img.scaleToHeight(600);
          this.canvas.add(img);
          const text = new fabric.Textbox("Write", {
            left: 250,
            top: 280,
            fontSize: 15,
            fontFamily: "Verdana",
            fill: "pink",
            editable: true
          });
          // const group = new fabric.Group([img, text], {
          //   left: 50,
          //   top: 50
          // });
          this.canvas.add(text);
          this.canvas.on("object:modified", () => {
            this.$emit("img-modified", this.canvas);
          });
        },
        { crossOrigin: "anonymous" }
      );
    }
  }
};
</script>

<style scoped>
canvas {
  border: red dashed 3px;
}
</style>