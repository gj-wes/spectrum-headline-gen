<template>
  <div class="item-container">
    <textarea class="text-input" rows="4" v-model.trim="inputText" @keyup="updateCanvas"></textarea>
    <div class="preview">
      <canvas class="preview-canvas" :ref="canvasRef"></canvas>
    </div>
    <input type="text" class="filename-input" v-model.trim="filenameText">
  </div>
</template>

<script>

export default {
  props: ['filename', 'id'],
  data() {
    return {
      inputText: '',
      filenameText: '',
      canvasContext: null,
      canvasWidth: null,
      canvasHeight: null,
      textWidth: null
    }
  },
  methods: {
    updateCanvas() {
      // clear canvas
      this.canvasContext.clearRect(0, 0, this.canvasWidth, this.canvasHeight)
      // add new text
      this.canvasContext.fillText(this.inputText, this.canvasWidth / 2, 0)
      // resize gradient
      this.textWidth = this.canvasContext.measureText(this.inputText).width;
      this.canvasContext.fillStyle = this.updateGradient();
    },
    updateGradient() {
      // set the gradient background
      const gradient = this.canvasContext.createLinearGradient(
        (this.canvasWidth / 2) - (this.textWidth / 2),
        this.canvasHeight / 2,
        (this.canvasWidth / 2) + (this.textWidth / 2),
        this.canvasHeight / 2,
      );
      gradient.addColorStop(0, "#ff9e00")
      gradient.addColorStop(0.25, "#ff0000")
      gradient.addColorStop(0.5, "#b5007d")
      gradient.addColorStop(0.75, "#21429c")
      gradient.addColorStop(1, "#0071ff")

      return gradient;
    }
  },
  computed: {
    canvasRef() {
      return `canvas-${this.id}`
    }
  },
  mounted() {
    this.filenameText = this.filename
    // set canvas dimensions
    this.canvasWidth = 1200
    this.canvasHeight = 68
    this.$refs[`canvas-${this.id}`].width = this.canvasWidth
    this.$refs[`canvas-${this.id}`].height = this.canvasHeight

    // get canvas context
    this.canvasContext = this.$refs[`canvas-${this.id}`].getContext('2d')

    // set font style and placeholder text
    this.canvasContext.font = "60px 'Sky Text'"
    this.canvasContext.textBaseline = "top"
    this.canvasContext.textAlign = "center"
    const textPlacehold = 'Lorem ipsum dolor';
    
    // apply gradient
    this.textWidth = this.canvasContext.measureText(textPlacehold).width;
    this.canvasContext.fillStyle = this.updateGradient();

    // add placehold text
    this.canvasContext.fillText(textPlacehold, this.canvasWidth / 2, 0)
  }
}
</script>

<style>
  .item-container {
    padding: 1rem;
    border-radius: 1rem;
    box-shadow: 0 1px 3px 1px rgba(0, 0, 0, 0.3);
  }
  .text-input {
    width: 100%;
    resize: none;
  }
  .preview {
    margin: 2rem 0;
  }
  .preview-canvas {
    width: 100%;
    height: auto;
    vertical-align: middle;
  }
  .filename-input {
    width: 100%;
  }
</style>