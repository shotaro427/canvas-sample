<template>
  <v-row justify="center" align="center" class="mt-10" :style="scaleStyle">
    <div id="sign_wrap">
      <canvas id="sign" ref="sign" width="680" height="200"/>
      <svg id="sign_focus" width="680" height="200" viewBox="0 0 680 200">
        <line
          v-for="n in 5"
          :key="`svg-line-x-${n}`"
          :x1="120 + (n - 1) * 100"
          :y1="100"
          :x2="160 + (n - 1) * 100"
          :y2="100"
          stroke="#cccccc"
          stroke-width="1"
        />
        <line
          v-for="n in 5"
          :key="`svg-line-y-${n}`"
          :x1="140 + (n - 1) * 100"
          :y1="80"
          :x2="140 + (n - 1) * 100"
          :y2="120"
          stroke="#cccccc"
          stroke-width="1"
        />
      </svg>
      <v-btn
        @touchstart="clearSign"
      >クリア</v-btn>
    </div>
  </v-row>
</template>
<script>
export default {
  props: {
    scale: {
      type: Number,
      default: 1,
    },
    isAfter: {
      type: Boolean,
      default: false,
    }
  },
  computed: {
    scaleStyle() {
      return {
        transform: `scale(${this.scale})`
      }
    }
  },
  mounted() {
    this.addEventCanvas()
  },
  beforeDestroy() {
    this.removeEventCanvas()
  },
  methods: {
    /**
     * サインキャンバスにタッチ開始(修正後)
     */
    afterTouchstartSign (e) {
      const xRatio = e.target.getBoundingClientRect().width / 680
      const yRatio = e.target.getBoundingClientRect().height / 200
      const x = e.touches[0].pageX - e.target.getBoundingClientRect().left
      const y = e.touches[0].pageY - e.target.getBoundingClientRect().top
      this.drawSignStart(x / xRatio, y / yRatio)
    },
    /**
     * サインキャンバスにタッチ移動(修正後)
     */
    afterTouchmoveSign (e) {
      const xRatio = e.target.getBoundingClientRect().width / 680
      const yRatio = e.target.getBoundingClientRect().height / 200
      const x = e.touches[0].pageX - e.target.getBoundingClientRect().left
      const y = e.touches[0].pageY - e.target.getBoundingClientRect().top
      this.drawSign(x / xRatio, y / yRatio)
    },
    /**
     * サインキャンバスにタッチ開始(修正前)
     */
    beforeTouchstartSign (e) {
      const x = e.touches[0].pageX - e.target.getBoundingClientRect().left
      const y = e.touches[0].pageY - e.target.getBoundingClientRect().top
      this.drawSignStart(x, y)
    },
    /**
     * サインキャンバスにタッチ移動(修正前)
     */
    beforeTouchmoveSign (e) {
      const x = e.touches[0].pageX - e.target.getBoundingClientRect().left
      const y = e.touches[0].pageY - e.target.getBoundingClientRect().top
      this.drawSign(x, y)
    },
    /**
     * キャンバスにイベントを登録する
     */
    addEventCanvas () {
      if (this.$refs.sign) {
        // キャンバス準備
        const canvas = this.$refs.sign
        if (this.isAfter) {
          canvas.addEventListener('touchstart', this.afterTouchstartSign)
          canvas.addEventListener('touchmove', this.afterTouchmoveSign)
        } else {
          canvas.addEventListener('touchstart', this.beforeTouchstartSign)
          canvas.addEventListener('touchmove', this.beforeTouchmoveSign)
        }

        this.signCtx = canvas.getContext('2d')
      } else {
        requestAnimationFrame(() => {
          this.addEventCanvas()
        })
      }
    },
    /**
     * キャンバスのイベントを破棄する
     */
    removeEventCanvas () {
      if (this.$refs.sign) {
        const canvas = this.$refs.sign
        if (this.isAfter) {
          canvas.removeEventListener('touchstart', this.afterTouchstartSign)
          canvas.removeEventListener('touchmove', this.afterTouchmoveSign)
        } else {
          canvas.removeEventListener('touchstart', this.beforeTouchstartSign)
          canvas.removeEventListener('touchmove', this.beforeTouchmoveSign)
        }
      }
    },
    /**
     * 描画開始
     */
    drawSignStart (x, y) {
      this.signCtx.beginPath()
      this.signCtx.moveTo(x, y)
    },
    /**
     * 描画
     */
    drawSign (x, y) {
      this.signCtx.lineTo(x, y)
      this.signCtx.stroke()
    },
    /**
     * サインをクリアする
     */
    clearSign () {
      this.signCtx.clearRect(0, 0, 680, 200)
    },
  },
}
</script>
<style lang="scss">
html {
  overflow: hidden;
  height: 100vh;
}
#sign_wrap {
  position: relative;
  #sign {
    background-color: #ffffff;
    border: 1px solid #cccccc;
  }
  svg {
    position: absolute;
    pointer-events: none;
    left: 0;
    top: 0;
  }
}
</style>
