<template>
  <div>
    <!-- <p>
      Track function:
      <select v-model="selected">
        <option v-for="option in options" :key="option.text" :value="option">
          {{ option.text }}
        </option>
      </select>
    </p> -->

    <qrcode-stream :paused="paused" @detect="onDetect" @camera-on="onCameraOn" @camera-off="onCameraOff" @error="onError">
      <div v-show="showScanConfirmation" class="scan-confirmation">
        <img src="../assets/checkmark.svg" alt="Checkmark" width="128" />
      </div>
    </qrcode-stream>

    <div class="result">
      <p>{{ result }}</p>
    </div>
  </div>
</template>

<style>
.qrcode {
  width: 300px;
  height: 300px;
  margin: 0 auto;
  display: block;
  flex: 1;
  border: 1px solid #ccc;
  border-radius: 4px;
  text-align: center;
  margin-bottom: 20px;
  margin-top: 50px;
}

@media (max-width: 480px) {
  .qrcode {
    width: 100%;
    height: auto;
    margin-top: 20px;
    margin-bottom: 20px;
  }
}

.scan-confirmation {
  position: absolute;
  width: 100%;
  height: 100%;

  background-color: rgba(255, 255, 255, 0.8);

  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
}
</style>



<script>
import { QrcodeStream } from 'vue-qrcode-reader'
import axios from 'axios'

export default {
  components: {
    QrcodeStream,
    // QrcodeDropZone,
    // QrcodeCapture
  },

  data() {
    return {
      paused: false,
      result: "",
      showScanConfirmation: false,
    }
  },

  methods: {
    onCameraOn() {
      this.showScanConfirmation = false
    },

    onCameraOff() {
      this.showScanConfirmation = true
    },
    // onDetect (code) {
    //   console.log('onDetect', code)
    // },

    onError: console.error,

    async onDetect(detectedCodes) {
      this.result = JSON.stringify(
        detectedCodes.map(code => code.rawValue)[0]
      )




      const data  = await axios.post(`https://offismekan-dev-api.arneca.com/api/staticQRs/checkQRImage`, {
        data: JSON.parse(this.result),
        // headers: {
        //   "Content-Type": "application/json"
        // }
      })
      console.log(data, "data")


      console.log(this.result, "result")

      this.paused = true
      await this.timeout(500)
      this.paused = false
    },

    timeout(ms) {
      return new Promise((resolve) => {
        window.setTimeout(resolve, ms)
      })
    },

    paintOutline(detectedCodes, ctx) {
      for (const detectedCode of detectedCodes) {
        const [firstPoint, ...otherPoints] = detectedCode.cornerPoints

        ctx.strokeStyle = 'red'

        ctx.beginPath()
        ctx.moveTo(firstPoint.x, firstPoint.y)
        for (const { x, y } of otherPoints) {
          ctx.lineTo(x, y)
        }
        ctx.lineTo(firstPoint.x, firstPoint.y)
        ctx.closePath()
        ctx.stroke()
      }
    },

    paintBoundingBox(detectedCodes, ctx) {
      for (const detectedCode of detectedCodes) {
        const {
          boundingBox: { x, y, width, height }
        } = detectedCode

        ctx.lineWidth = 2
        ctx.strokeStyle = '#007bff'
        ctx.strokeRect(x, y, width, height)
      }
    },

    paintCenterText(detectedCodes, ctx) {
      for (const detectedCode of detectedCodes) {
        const { boundingBox, rawValue } = detectedCode

        const centerX = boundingBox.x + boundingBox.width / 2
        const centerY = boundingBox.y + boundingBox.height / 2

        const fontSize = Math.max(12, (50 * boundingBox.width) / ctx.canvas.width)
        console.log(boundingBox.width, ctx.canvas.width)

        ctx.font = `bold ${fontSize}px sans-serif`
        ctx.textAlign = 'center'

        ctx.lineWidth = 3
        ctx.strokeStyle = '#35495e'
        ctx.strokeText(detectedCode.rawValue, centerX, centerY)

        ctx.fillStyle = '#5cb984'
        ctx.fillText(rawValue, centerX, centerY)
      }
    },

    logErrors: console.error
  }
}
</script>