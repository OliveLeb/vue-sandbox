<template>
  <div class="recorder">
    <div class="recorder__controls">
      <button @click="play">Play</button>
      <button @click="pause">Pause</button>
      <button @click="stop">Stop</button>
    </div>
    <div class="recorder__waveform">
      <canvas ref="canvas" />
    </div>
  </div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue'
import * as d3 from 'd3'

const recorder = ref(null)
const stream = ref(null)
const chunks = ref([])
const context = ref(null)

onMounted(async () => {
  stream.value = await navigator.mediaDevices.getUserMedia({ audio: true })
  console.log(stream.value)
  if (stream.value) {
    recorder.value = new MediaRecorder(stream.value)
    console.log(recorder.value)
    recorder.value.onstart = () => {
      console.log('start')
    }
    recorder.value.onpause = () => console.log('pause')
    recorder.value.ondataavailable = ({ data }) => {
      if (data !== undefined && data.size !== 0) {
        chunks.value.push(data)
        const recording = new Blob(chunks.value, { type: 'audio/webm' })
        console.log(recording)

        if (!recording) {
          return
        }
        renderWaveform(recording)
      }
    }
  }

  context.value = new AudioContext()
})
const canvas = ref(null)
onBeforeUnmount(() => {
  console.log(stream.value)
})

const play = () => {
  recorder.value.start(2100)
}
const pause = () => {
  recorder.value.pause()
}
const stop = () => {
  recorder.value.stop()
  stream.value.getTracks().forEach((track) => track.stop())
}

const sliceAudio = (buffer, start, end) => {
  const chunk =
    start + end === buffer.byteLength ? buffer : buffer.slice(start, end)
  const blob = new Blob([new Uint8Array(chunk)])
  const reader = new FileReader()

  reader.readAsArrayBuffer(blob)

  return new Promise((resolve, reject) => {
    reader.onloadend = (e) => {
      const buffer = e.target.result

      context.value
        .decodeAudioData(buffer)
        .then((decodedBuffer) => resolve(decodedBuffer))
        .catch((err) => reject(err))
    }
  })
}

const getArrayBuffer = (blob) => {
  const reader = new FileReader()
  reader.readAsArrayBuffer(blob)

  return new Promise((resolve, reject) => {
    reader.onerror = (err) => reject(err)
    reader.onloadend = (e) => resolve(e.target.result)
  })
}
const getAudioBuffers = async (buffer) => {
  const bufferLength = buffer.byteLength
  const chunkLength = bufferLength > 524000 ? 524000 : bufferLength

  let start = 0
  let end = start + chunkLength

  const slice = (buffer, start, end) => {
    async function* gen() {
      while (start < bufferLength) {
        const decodedBuffer = await sliceAudio(buffer, start, end)
        yield decodedBuffer

        start += chunkLength
        end =
          start + chunkLength > bufferLength
            ? bufferLength
            : start + chunkLength
      }
    }

    return gen()
  }

  const audioBuffers = []

  for await (const decodedBuffer of slice(buffer, start, end)) {
    audioBuffers.push(decodedBuffer)
  }

  return audioBuffers
}

const getWaveformData = (buffers) => {
  const dataArrays = buffers.map((buffer) => buffer.getChannelData(0))
  const totalLength = dataArrays.reduce((total, data) => total + data.length, 0)
  const channelData = new Float32Array(totalLength)

  let offset = 0

  dataArrays.forEach((data) => {
    channelData.set(data, offset)
    offset += data.length
  })

  return channelData
}

const audioBuffers = ref(null)
const duration = ref(null)
const renderWaveform = async (file) => {
  const buffer = await getArrayBuffer(file)
  const audioBuffers = await getAudioBuffers(buffer)

  audioBuffers.value = audioBuffers
  duration.value = audioBuffers.reduce(
    (total, buffer) => total + buffer.duration,
    0
  )
  const ctx = canvas.value.getContext('2d')
  console.log(ctx)
  // this.showTotalTime(this.duration)

  const channelData = getWaveformData(audioBuffers)
  const drawLines = 200
  const totallength = channelData.length
  const eachBlock = Math.floor(totallength / drawLines)
  const lineGap = 300 / drawLines

  // this.canvases.forEach((canvas) => {
  ctx.save()
  ctx.fillStyle = 'transparent'
  ctx.fillRect(0, 0, 300, 100)
  ctx.strokeStyle = 'transparent'
  ctx.translate(0, 100 / 2)
  ctx.lineWidth = 1
  ctx.beginPath()

  for (let i = 0; i <= drawLines; i++) {
    const audioBuffKey = Math.floor(eachBlock * i)
    const x = i * lineGap
    const y = channelData[audioBuffKey] * 100 * 0.8

    ctx.moveTo(x, y)
    ctx.strokeStyle = '#ff0000'
    // ctx.lineTo(x, y * -1)
    ctx.roundRect(0, 0, x, y * -1)
  }

  // ctx.stroke()
  // ctx.restore()
  // ctx.strokeStyle = '#ff0000'
  // ctx.moveTo(0, 100 / 2)
  // ctx.lineTo(300, 100 / 2)
  // ctx.roundRect(0, 0, 20, 20)
  // ctx.stroke()
  // ctx.restore()
  // })
}
</script>

<style>
.recorder__controls {
  display: flex;
}
</style>
