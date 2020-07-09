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
      backgroundImage: null
    };
  },
  mounted() {
    this.canvas = new fabric.Canvas(this.$refs.canvas);
    // http://fabricjs.com/fabric-gotchas
    fabric.Object.NUM_FRACTION_DIGITS = 8;
  },
  methods: {
    async setBackgroundImage() {
      this.backgroundImage = await getImage(this.imgUrl).catch(err =>
        console.log("err", err)
      );
      const widthAspectRatio = this.backgroundImage.height / this.backgroundImage.width;
      this.canvas.setWidth(this.height / widthAspectRatio);
      this.canvas.setHeight(this.height);
      const imgDimensions = {
        scaleX: this.canvas.getWidth() / this.backgroundImage.width,
        scaleY: this.canvas.getHeight() / this.backgroundImage.height
      };
      this.canvas.setBackgroundImage(
        this.backgroundImage,
        this.canvas.renderAll.bind(this.canvas),
        imgDimensions
      );
    },
    async exportImage() {
      const json = this.canvas.toJSON();
      const exportScaleX = this.canvas.getWidth() / this.backgroundImage.width;
      const exportScaleY = this.height / this.backgroundImage.height;
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
        this.canvas.setWidth(this.backgroundImage.width);
        this.canvas.setHeight(this.backgroundImage.height);
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
        fontSize: 40,
        fill: "red",
        top: 0.5 * this.canvas.getHeight(),
        left: 0.5 * this.canvas.getWidth(),
        originX: 'center',
        originY: 'center',
        lockUniScaling: true,
        textAlign: 'center',
        width: this.canvas.getWidth() * 0.50
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