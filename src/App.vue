<template>
  <div class="antialiased text-gray-900 px-6">
    <div class="max-w-7xl mx-auto py-12">
      <div class="text-center">
          <h1 class="text-4xl font-semibold">Video to GIF converter</h1>
          <h3 class="text-lg">Vue + FFMPEG.WASM + Tailwind CSS</h3>
      </div>
      <div v-if="!isSupported" class="text-xl text-center mt-5">Sorry, ffmpeg.wasm is currently supported only in Chrome desktop 😔</div>
      <div v-else class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-6">
          <div :class="`bg-white rounded md:shadow p-4 ${!videoSrc && 'h-60'} flex flex-col items-center justify-center`">
              <video v-if="videoSrc" controls :src="videoSrc" class="mb-4"></video>
              <input ref="fileInput" @change="updatePreview" type="file" class="hidden" accept="video/*" />
              <div>
                <Button :disabled="!isReady || isConverting" @click="$refs.fileInput.click()" :text="isReady ? 'Select Video File' : 'Loading ffmpeg...'" />
                <Button class="ml-2" v-if="videoSrc" :disabled="isConverting" @click="convertToGIF(video)" :text="isConverting ? 'Converting Video...' : 'Convert To GIF'" />
              </div>
          </div>
          <div class="bg-white rounded md:shadow p-4">
            <div class="flex items-center justify-center h-full">
              <div v-if="imageSrc" class="text-center">
                <img :src="imageSrc" alt="output gif" class="mb-4" />
                <Button @click="downloadBlob" text="Download GIF" />
              </div>
              <div v-else class="text-gray-500 text-xl">Output goes here</div>
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

import useFFmpeg from './hooks/useFFmpeg'
import Button from './components/Button.vue'

export default {
  name: 'App',
  components: { Button },
  setup() {
    const videoSrc = ref(null)
    const video = ref(null)

    const {
      isReady,
      convertToGIF,
      output: imageSrc,
      isConverting,
      isSupported
    } = useFFmpeg()

    const updatePreview = (e) => {
      if (e.target.files.length) {
        videoSrc.value = URL.createObjectURL(e.target.files[0])
        video.value = e.target.files[0]
      }
    }

    const downloadBlob = () => {
      const saveImg = document.createElement('a')
      saveImg.href = imageSrc.value
      saveImg.download = `${Date.now()}.gif`
      saveImg.click()
    }
    
    return {
      videoSrc,
      updatePreview,
      imageSrc,
      isConverting,
      isReady,
      convertToGIF,
      downloadBlob,
      video,
      isSupported
    }
  }
}
</script>
