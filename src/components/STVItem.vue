<script setup>
import { reactive, ref, computed, watch, onMounted } from 'vue';

const props = defineProps({
  filename: String,
  id: Number
})

const inputText = ref('')
const filenameText = ref('')

const canvasRef = ref(null)
const canvas = reactive({
  context: null,
  width: null,
  height: null
})
const text = reactive({
  font: null,
  lineHeight: null,
  lineCount: 1,
  textWidth: null
})

onMounted(() => {
  filenameText.value = props.filename

  // set canvas dimensions
  if (props.filename.endsWith('--mb')) {
    canvas.width = 640
    text.lineHeight = 50
    canvas.height = text.lineHeight * text.lineCount
    text.font = "48px 'Sky Text'"
  } else {
    canvas.width = 1200
    text.lineHeight = 68
    canvas.height = text.lineHeight * text.lineCount
    text.font = "60px 'Sky Text'"
  }
})

const gradient = computed(() => {
  const gradientSetting = canvas.context.createLinearGradient(
    (canvas.width / 2) - (text.textWidth / 2),
    canvas.height / 2,
    (canvas.width / 2) + (text.textWidth / 2),
    canvas.height / 2,
  );
  gradientSetting.addColorStop(0, "#ff9e00")
  gradientSetting.addColorStop(0.35, "#ff0000")
  gradientSetting.addColorStop(0.6, "#b5007d")
  gradientSetting.addColorStop(0.85, "#21429c")
  gradientSetting.addColorStop(1, "#0071ff")

  return gradientSetting;
})

const downloadUrl = ref('')
const updateDownloadUrl = () => {
  downloadUrl.value = canvasRef.value.toDataURL('image/png')
}

const updateCanvas = () => {
  // get canvas context
  canvas.context = canvasRef.value.getContext('2d')

  // clear canvas
  canvas.context.clearRect(0, 0, canvas.width, canvas.height)

  // set font style
  canvas.context.font = text.font
  canvas.context.textBaseline = "top"
  canvas.context.textAlign = "center"

  const lines = inputText.value.split('\n')
  text.lineCount = lines.length

  canvas.height = text.lineHeight * text.lineCount

  const longest = lines.reduce((longest, currentWord) => {
    return currentWord.length > longest.length ? currentWord : longest;
  }, "");
  let linePosition = 0;

  for (const line of lines) {
    // set textWidth to length of longest line
    if (line.length === longest.length) {
      text.textWidth = canvas.context.measureText(line).width;
    }
    canvas.context.fillStyle = gradient.value;
    
    canvas.context.fillText(line, canvas.width / 2, linePosition)
    linePosition += text.lineHeight

  }

  updateDownloadUrl()
}

watch(inputText, () => updateCanvas())

const hascopy = computed(() => inputText.value !== '' ? true : false)
</script>

<template>
  <div class="item-container">
    <textarea class="text-input" rows="4" v-model.trim="inputText" @keyup.enter="updateCanvas"></textarea>
    <div class="preview">
      <a :href="downloadUrl" :download="filenameText" :hascopy="hascopy" class="download">
        <canvas 
          class="preview-canvas" 
          ref="canvasRef" 
          :width="canvas.width" 
          :height="canvas.height"
        ></canvas>
      </a>
    </div>
    <input type="text" class="filename-input" v-model.trim="filenameText">
  </div>
</template>

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
  canvas {
    border: 1px solid gray;
  }
  .filename-input {
    width: 100%;
    text-align: center;
  }
</style>