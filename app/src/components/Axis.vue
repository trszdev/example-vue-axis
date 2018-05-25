<template>
  <div class="container">
    <p class="formula">
      <span :class="!firstNumberIsValid && 'highlight'">{{firstNumberExpected}}</span> +
      <span :class="!secondNumberIsValid && 'highlight'">{{secondNumberExpected}}</span> =
      <input :maxlength="2" v-model="sum" :pattern="`1|${sumExpected}`"
        :readonly="state != 2" :style="`width: 2ch`" />
    </p>
    <div class="ruler">
      <div class="arc" :style="firstNumberArcStyle">
        <div class="number">
          <input :maxlength="1" v-model="firstNumber"
            :pattern="firstNumberExpected" :readonly="state > 0" />
        </div>
        <canvas ref="firstArcCanvas" />
      </div><!--
      --><div class="arc" :style="secondNumberArcStyle" :class="state === 0 && 'hidden'">
        <div class="number">
          <input :maxlength="1" v-model="secondNumber"
            :pattern="secondNumberExpected" :readonly="state > 1" />
        </div>
        <canvas ref="secondArcCanvas" />
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import { Component, Watch } from 'vue-property-decorator'

const getRandomInt = (from, to) => from + Math.round(Math.random() * (to - from))
const PIXELS_PER_ONE_RULER_UNIT = 39

@Component
export default class Axis extends Vue {
  firstNumber = ''
  secondNumber = ''
  sum = '?'

  firstNumberIsValid = true
  secondNumberIsValid = true

  getArcStyle = numberStr => `
    width: ${PIXELS_PER_ONE_RULER_UNIT * numberStr}px;
    height: ${8 * (+numberStr + 1)}px
    `

  get firstNumberArcStyle() {
    return this.getArcStyle(this.firstNumberExpected)
  }

  get secondNumberArcStyle() {
    return this.getArcStyle(this.secondNumberExpected)
  }

  @Watch('firstNumber') onFirstNumberChanged() {
    this.firstNumberIsValid = this.firstNumberExpected.startsWith(this.firstNumber)
  }

  @Watch('secondNumber') onSecondNumberChanged() {
    this.secondNumberIsValid = this.secondNumberExpected.startsWith(this.secondNumber)
  }

  mounted() {
    const { firstArcCanvas, secondArcCanvas } = this.$refs
    this.updateCanvas(firstArcCanvas)
    this.updateCanvas(secondArcCanvas)
  }

  updateCanvas = (canvas) => {
    canvas.width = canvas.offsetWidth // eslint-disable-line no-param-reassign
    canvas.height = canvas.offsetHeight // eslint-disable-line no-param-reassign
    const { width, height } = canvas
    const ctx = canvas.getContext('2d')
    ctx.lineWidth = 2
    ctx.strokeStyle = 'purple'
    ctx.clearRect(0, 0, width, height)
    ctx.beginPath()
    ctx.moveTo(0, height)
    ctx.quadraticCurveTo(width / 2, -height + 10, width, height)
    ctx.moveTo(width, height)
    ctx.lineTo(width - 13, height - 22)
    ctx.moveTo(width, height)
    ctx.lineTo(width - 20, height - 5)
    ctx.stroke()
  }

  @Watch('state') onStateChanged() {
    if (this.state === 2) {
      this.sum = ''
    }
  }

  firstNumberExpected = '7'
  secondNumberExpected = '5'
  sumExpected = '12'

  created() {
    const a = getRandomInt(6, 9)
    const b = getRandomInt(11 - a, 14 - a)
    this.firstNumberExpected = String(a)
    this.secondNumberExpected = String(b)
    this.sumExpected = String(a + b)
  }

  get state() {
    return (this.firstNumber === this.firstNumberExpected ? 1 : 0) +
      (this.secondNumber === this.secondNumberExpected ? 1 : 0) +
      (this.sum === this.sumExpected ? 1 : 0)
  }
}
</script>

<style>
body {
  margin: 0;
  font-family: Arial;
  font-size: 15px;
}

.highlight {
  border-radius: 5px;
  background: orange;
}

.number {
  position: absolute;
  top: -30px;
  left: 50%;
  margin-left: -12px;
}

input {
  border: 2px solid #ccc;
  border-radius: 5px;
  width: 1ch;
  padding: 4px;
  font-size: 100%;
  font-weight: inherit;
  outline: none;
}

input:invalid {
  color: red;
}

input[readonly] {
  border-color: white;
}

.container {
  display: flex;
  width: 100%;
  height: 100vh;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.arc:first-child {
  margin-left: 35px;
}

.arc {
  position: relative;
  display: inline-block;
  margin-top: -80px;
}

.hidden {
  visibility: hidden;
}

canvas {
  position: absolute;
  height: 100%;
  width: 100%;
  bottom: 0;
  left: 0;
}


.formula {
  font-size: 200%;
  font-weight: bold;
  margin-bottom: 300px;
}

.ruler {
  width: 875px;
  background: url(../assets/sprite.png);
  height: 83px;
}

</style>
