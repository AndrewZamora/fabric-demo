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
      const json = this.canvas.toJSON();
      json.backgroundImage.scaleX = 1;
      json.backgroundImage.scaleY = 1;
      this.canvas.clear();
      this.canvas.renderAll();
      this.canvas.loadFromJSON(json, () => {
        this.canvas.setWidth(1275);
        this.canvas.setHeight(1700);
        this.canvas.renderAll();
        this.canvas.getElement().toBlob(blob => {
          const blobUrl = URL.createObjectURL(blob);
          const link = document.createElement("a");
          link.href = blobUrl;
          link.download = name;
          link.click();
        });
      });
    },
    addTextInput() {
      const textInput = new fabric.Textbox("Tap And Type", {
        width: 200,
        height: 200,
        top: 250,
        left: 5,
        fontSize: 20,
        fill: "pink",
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