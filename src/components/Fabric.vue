<template>
  <div>
    <canvas ref="canvas" :height="height" :width="width"></canvas>
    <button @click="setBackgroundImage()">Add Background</button>
    <button @click="exportImage">Export Image</button>
    <button @click="addTextInput">Add Text</button>
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
      outputImg: null,
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
      this.inputImg = await getImage(this.imgUrl).catch(err =>
        console.log("err", err)
      );
      this.outputImg = JSON.stringify(this.inputImg);
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
    async exportImage() {
      // const canvas = document.createElement("canvas");
      // canvas.width = this.outputImg.width;
      // canvas.height = this.outputImg.height;
      // const exportCanvas = new fabric.Canvas(canvas);
      // const image = await getImage(this.imgUrl);
      // exportCanvas.add(image);
      // exportCanvas.centerObject(image);
      // exportCanvas.renderAll();
      // console.log(this.canvas);
      // console.log(exportCanvas);
      const originalDimensions = JSON.parse(this.outputImg)
      this.canvas.backgroundImage.height = originalDimensions.height;
      this.canvas.backgroundImage.height = originalDimensions.width;
      this.canvas.backgroundImage.scaleX = 1;
      this.canvas.backgroundImage.scaleY = 1;
      this.canvas.setHeight(originalDimensions.height);
      this.canvas.setWidth(originalDimensions.width);
      console.log(this.canvas);
      this.canvas.getElement().toBlob(blob => {
        const blobUrl = URL.createObjectURL(blob);
        const link = document.createElement("a");
        link.href = blobUrl;
        link.download = name;
        link.click();
      });
    },
    addTextInput() {
      const textInput = new fabric.Textbox("Tap And Type", {
        width: 200,
        height: 200,
        top: 250,
        left: 5,
        fontSize: 20,
        fill: 'pink',
        textAlign: "center"
      });
      textInput.enterEditing();
      textInput.hiddenTextarea.focus();
      this.canvas.add(textInput);
    }
  }
};
</script>

<style scoped>
canvas {
  border: red dashed 3px;
}
</style>