<!-- CameraCapture.vue -->

<template>
  <div>
    <div class="video-container">
      <div class="moldura"></div>

      <video ref="video" width="640" height="480" autoplay></video>
    </div>
    <button @click="capture">Capturar Selfie</button>
    <canvas ref="canvas" style="display: none"></canvas>
    <img v-if="capturedImage" :src="capturedImage" alt="Selfie Capturada" />
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const capturedImage = ref(null)

    const setupCamera = async () => {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true })
        video.value.srcObject = stream
      } catch (error) {
        console.error('Erro ao acessar a câmera:', error)
      }
    }

    const capture = () => {
      const videoElement = video.value
      const canvasElement = canvas.value

      canvasElement.width = videoElement.videoWidth
      canvasElement.height = videoElement.videoHeight

      const context = canvasElement.getContext('2d')
      context.drawImage(videoElement, 0, 0, canvasElement.width, canvasElement.height)

      const imageDataUrl = canvasElement.toDataURL('image/png')
      capturedImage.value = imageDataUrl

      // Desativar a webcam após a captura
      const stream = videoElement.srcObject
      const tracks = stream.getTracks()
      tracks.forEach((track) => track.stop())
      videoElement.srcObject = null
    }

    // Use onMounted to garantir que o componente esteja montado antes de acessar o DOM
    onMounted(setupCamera)

    // Refs para elementos DOM
    const video = ref(null)
    const canvas = ref(null)

    return { video, canvas, capturedImage, capture }
  }
}
</script>

<style scoped>
/* Estilo para o contêiner que envolve o vídeo e a moldura */
.video-container {
  position: relative;
  width: 100%;
  max-width: 800px; /* Defina a largura máxima conforme necessário */
  margin: auto;
}

/* Estilo para a moldura */
.moldura {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 5px solid #fff; /* Cor e espessura da moldura */
  box-sizing: border-box;
}

/* Estilo para o vídeo para garantir que ele esteja acima da moldura */
video {
  width: 100%;
  height: auto;
  display: block;
}
</style>
