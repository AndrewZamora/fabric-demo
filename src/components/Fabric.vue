<template>
  <div>
    <canvas ref="canvas" :height="height" :width="width"></canvas>
    <button @click="setBackgroundImage()">Add Background</button>
    <button @click="exportImage" id="btn">Export Image</button>
  </div>
</template>

<script>
import { fabric } from "fabric";
const getImage = url => {
  return new Promise(resolve => {
    fabric.Image.fromURL(
      url,
      img => {
        resolve(img);
      },
      { crossOrigin: "anonymous" }
    );
  });
};
export default {
  props: {
    imgUrl: String,
    height: Number,
    width: Number
  },
  data() {
    return {
      canvas: null,
      inputImg: null,
      outputImg: null
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
          this.canvas.add(img);
          const text = new fabric.Textbox("Write", {
            left: 250,
            top: 280,
            fontSize: 15,
            fontFamily: "Verdana",
            fill: "pink",
            editable: true
          });
          this.canvas.add(text);
          this.canvas.on("object:modified", () => {
            this.$emit("img-modified", this.canvas);
          });
        },
        { crossOrigin: "anonymous" }
      );
    },
    async setBackgroundImage() {
      this.outputImg = await getImage(this.imgUrl).catch(err => {
        console.log(err);
      });
      this.inputImg = await getImage(this.imgUrl).catch(err =>
        console.log("err", err)
      );
      const imgDimensions = {
        scaleX: this.width / this.inputImg.width,
        scaleY: this.height / this.inputImg.height
      };
      this.canvas.setBackgroundImage(
        this.inputImg,
        this.canvas.renderAll.bind(this.canvas),
        imgDimensions
      );
    },
    exportImage() {
      const canvas = document.createElement("canvas");
      canvas.width = this.outputImg.width;
      canvas.height = this.outputImg.height;
      const exportCanvas = new fabric.Canvas(canvas);
      const image = this.outputImg;
      exportCanvas.add(image);
      exportCanvas.centerObject(image);
      exportCanvas.renderAll();

      exportCanvas.getElement().toBlob(blob => {
        const blobUrl = URL.createObjectURL(blob);
        const link = document.createElement("a");
        link.href = blobUrl;
        link.download = name;
        link.click()
      });
    }
  }
};
</script>

<style scoped>
canvas {
  border: red dashed 3px;
}
</style>