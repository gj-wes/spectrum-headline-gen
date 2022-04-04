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

      // check for multiple lines
      if (this.inputText.includes('\n')) {

        // add multiple lines
        const lines = this.inputText.split('\n')

        const longest = lines.reduce((longest, currentWord) => {
          return currentWord.length > longest.length ? currentWord : longest;
        }, "");
        let linePosition = 0;

        for (const line of lines) {
          this.canvasContext.fillText(line, this.canvasWidth / 2, linePosition)
          linePosition += 48

          // set textWidth to length of longest line
          if (line.length === longest.length) {
            this.textWidth = this.canvasContext.measureText(line).width;
          }
        }

      } else {

        // add single line
        this.canvasContext.fillText(this.inputText, this.canvasWidth / 2, 0)
        // resize textWidth for gradient
        this.textWidth = this.canvasContext.measureText(this.inputText).width;
      }

      
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
    let fontSize = "";
    // set canvas dimensions
    if (this.filename.endsWith('--mb')) {
      this.canvasWidth = 640
      this.canvasHeight = 96
      fontSize = "46px 'Sky Text'"
    } else {
      this.canvasWidth = 1200
      this.canvasHeight = 68
      fontSize = "60px 'Sky Text'"
    }
    this.$refs[`canvas-${this.id}`].width = this.canvasWidth
    this.$refs[`canvas-${this.id}`].height = this.canvasHeight

    // get canvas context
    this.canvasContext = this.$refs[`canvas-${this.id}`].getContext('2d')

    // set font style and placeholder text
    this.canvasContext.font = fontSize
    this.canvasContext.textBaseline = "top"
    this.canvasContext.textAlign = "center"
    const textPlacehold = 'Lorem ipsum dolor'
    
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

    display: grid;
    grid-template-columns: 1fr;
    grid-template-rows: 66px auto 22px;
  }
  .text-input {
    width: 100%;
    resize: none;
    text-align: center;
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
    text-align: center;
  }
</style>