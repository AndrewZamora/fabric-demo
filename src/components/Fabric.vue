<template>
  <div class="fabric-container">
    <canvas ref="canvas"></canvas>
    <div class="fabric-btn-container">
      <button @click="setBackgroundImage()">Add Background</button>
      <button @click="exportImage">Export Image</button>
      <button @click="addTextInput">Add Text</button>
    </div>
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
    // http://fabricjs.com/fabric-gotchas
    fabric.Object.NUM_FRACTION_DIGITS = 8;
  },
  methods: {
    async setBackgroundImage() {
      this.inputImg = await getImage(this.imgUrl).catch(err =>
        console.log("err", err)
      );
      this.outputImg = JSON.stringify(this.inputImg);
      const widthAspectRatio =this.inputImg.height / this.inputImg.width;
      this.canvas.setWidth(this.height / widthAspectRatio);
      this.canvas.setHeight(this.height);
      const imgDimensions = {
        scaleX: this.canvas.getWidth() / this.inputImg.width,
        scaleY: this.canvas.getHeight() / this.inputImg.height
      };
      this.canvas.setBackgroundImage(
        this.inputImg,
        this.canvas.renderAll.bind(this.canvas),
        imgDimensions
      );
    },
    async exportImage() {
      const json = this.canvas.toJSON();
      const exportScaleX = this.canvas.getWidth() / JSON.parse(this.outputImg).width;
      const exportScaleY = this.height / JSON.parse(this.outputImg).height;
      json.objects.forEach(object => {
        if (object.type === "textbox") {
          object.scaleX = object.scaleX / exportScaleX;
          object.scaleY = object.scaleY / exportScaleY;
          object.left = object.left / exportScaleX;
          object.top = object.top / exportScaleY;
        }
      });
      json.backgroundImage.scaleX = 1;
      json.backgroundImage.scaleY = 1;
      this.canvas.clear();
      this.canvas.renderAll();
      this.canvas.loadFromJSON(json, () => {
        this.canvas.setWidth(JSON.parse(this.outputImg).width);
        this.canvas.setHeight(JSON.parse(this.outputImg).height);
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
      const textInput = new fabric.Textbox("Click And Type", {
        width: 200,
        height: 200,
        top: 250,
        left: 5,
        fontSize: 20,
        fill: "white",
        textAlign: "center",
        lockUniScaling: true
      });
      this.canvas.add(textInput);
    }
  }
};
</script>

<style scoped>
canvas {
  border: red dashed 3px;
}
.fabric-container {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
.fabric-btn-container {
  padding: 20px 0;
}
</style>