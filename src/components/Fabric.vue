<template>
  <div class="fabric-container">
    <div class="fabric-btn-container">
      <button @click="setBackgroundImage()">Add Background</button>
      <button @click="exportImage">Export Image</button>
      <button @click="addTextInput">Add Text</button>
      <button @click="changeTextColor"> Change Color</button>
    </div>
    <canvas ref="canvas"></canvas>
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
const loadFromJSON = (canvas, json) => {
  return new Promise(resolve => {
    canvas.loadFromJSON(json, () => {
      resolve();
    });
  });
};
const getBlob = canvas => {
  return new Promise(resolve => {
    canvas.getElement().toBlob(blob => resolve(blob));
  });
};
export default {
  props: {
    imgUrl: String,
    height: Number,
    width: Number,
    textSettings: Object,
    activeTextBoxColor: Number
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
      const widthAspectRatio =
        this.backgroundImage.height / this.backgroundImage.width;
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
      await loadFromJSON(this.canvas, json);
      this.canvas.setWidth(this.backgroundImage.width);
      this.canvas.setHeight(this.backgroundImage.height);
      this.canvas.renderAll();
      this.$emit("export", true);
      const blob = await getBlob(this.canvas);
      const blobUrl = await URL.createObjectURL(blob);
      this.$emit("blob", blobUrl);
      this.$emit("export", false);
    },
    addTextInput() {
      let defaultTextSettings = {
        fontSize: 40,
        fill: "black",
        top: 0.5 * this.canvas.getHeight(),
        left: 0.5 * this.canvas.getWidth(),
        originX: "center",
        originY: "center",
        lockUniScaling: true,
        textAlign: "center",
        width: 0.5 * this.canvas.getWidth()
      };
      if (this.textSettings) {
        Object.keys(this.textSettings).forEach(key => {
          defaultTextSettings[key] = this.textSettings[key];
        });
      }
      const textInput = new fabric.Textbox(
        "Click And Type",
        defaultTextSettings
      );
      this.canvas.add(textInput);
    },
    changeTextColor() {
      this.canvas.getActiveObject().set("fill",'purple');
      this.canvas.renderAll();
    }
  }
};
</script>

<style scoped>
</style>