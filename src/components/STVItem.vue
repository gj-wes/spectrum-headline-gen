<template>
  <div class="item-container">
    <textarea class="text-input" rows="4" v-model.trim="inputText" @keyup.enter="updateCanvas"></textarea>
    <div class="preview">
      <a :href="downloadUrl" :download="filenameText" :hascopy="hascopy" class="download">
        <canvas 
          class="preview-canvas" 
          :ref="canvasRef" 
          :width="canvasWidth" 
          :height="canvasHeight"
        ></canvas>
      </a>
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
      font: null,
      lineHeight: null,
      lineCount: 1,
      textWidth: null,
      downloadUrl: ''
    }
  },
  methods: {
    updateCanvas() {
      // get canvas context
      this.canvasContext = this.$refs[this.canvasRef].getContext('2d')

      // clear canvas
      this.canvasContext.clearRect(0, 0, this.canvasWidth, this.canvasHeight)

      // set font style
      this.canvasContext.font = this.font
      this.canvasContext.textBaseline = "top"
      this.canvasContext.textAlign = "center"

      const lines = this.inputText.split('\n')
      this.lineCount = lines.length

      this.canvasHeight = this.lineHeight * this.lineCount

      const longest = lines.reduce((longest, currentWord) => {
        return currentWord.length > longest.length ? currentWord : longest;
      }, "");
      let linePosition = 0;

      for (const line of lines) {
        // set textWidth to length of longest line
        if (line.length === longest.length) {
          this.textWidth = this.canvasContext.measureText(line).width;
        }
        this.canvasContext.fillStyle = this.gradient;
        
        this.canvasContext.fillText(line, this.canvasWidth / 2, linePosition)
        linePosition += this.lineHeight

      }

      this.updateDownloadUrl()
    },
    updateDownloadUrl() {
      this.downloadUrl = this.$refs[this.canvasRef].toDataURL('image/png')
    }
  },
  computed: {
    canvasRef() {
      return `canvas-${this.id}`
    },
    gradient() {
      const gradient = this.canvasContext.createLinearGradient(
        (this.canvasWidth / 2) - (this.textWidth / 2),
        this.canvasHeight / 2,
        (this.canvasWidth / 2) + (this.textWidth / 2),
        this.canvasHeight / 2,
      );
      gradient.addColorStop(0, "#ff9e00")
      gradient.addColorStop(0.35, "#ff0000")
      gradient.addColorStop(0.6, "#b5007d")
      gradient.addColorStop(0.85, "#21429c")
      gradient.addColorStop(1, "#0071ff")

      return gradient;
    },
    hascopy() {
      if (this.inputText !== '') return true;
      return false;
    }
  },
  watch: {
    inputText() {
      this.updateCanvas()
    }
  },
  mounted() {
    this.filenameText = this.filename
    // set canvas dimensions
    if (this.filename.endsWith('--mb')) {
      this.canvasWidth = 640
      this.lineHeight = 50
      this.canvasHeight = this.lineHeight * this.lineCount
      this.font = "48px 'Sky Text'"
    } else {
      this.canvasWidth = 1200
      this.lineHeight = 68
      this.canvasHeight = this.lineHeight * this.lineCount
      this.font = "60px 'Sky Text'"
    }
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
  .dark .item-container {
    box-shadow: 0 1px 3px 1px rgba(255, 255, 255, 0.3);
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