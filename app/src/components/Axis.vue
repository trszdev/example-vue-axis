<template>
  <div class="container">
    <p class="formula">
      <span :class="firstHasFocus && 'highlight'">{{firstNumberExpected}}</span> +
      <span :class="secondHasFocus && 'highlight'">{{secondNumberExpected}}</span> =
      <input v-if="state === 2" ref="sumInput" :maxlength="sumExpected.length"
        v-model="sum" :pattern="sumExpected" :style="`width: ${sumExpected.length}ch`" />
      <span v-else-if="state > 2">{{sumExpected}}</span>
      <span v-else-if="state < 2">?</span>
    </p>
    <div class="ruler">
      <div class="arc" :style="`width: ${39 * firstNumberExpected}px;
        height: ${8 * (+ firstNumberExpected + 1)}px`">
        <div class="number">
          <input v-if="state === 0" :style="`width: ${firstNumberExpected.length}ch`"
            ref="firstNumberInput" :maxlength="firstNumberExpected.length"
            v-model="firstNumber" :pattern="firstNumberExpected"
            @focus="() => firstHasFocus = true" @blur="() => firstHasFocus = false"/>
          <span v-else>{{firstNumber}}</span>
        </div>
      </div><!--
      --><div class="arc" v-if="state > 0" :style="`width: ${39 * secondNumberExpected}px;
        height: ${8 * (+ secondNumberExpected + 1)}px`">
        <div class="number">
          <input v-if="state === 1" :style="`width: ${secondNumberExpected.length}ch`"
            ref="secondNumberInput" :maxlength="secondNumberExpected.length"
            v-model="secondNumber" :pattern="secondNumberExpected"
            @focus="() => secondHasFocus = true" @blur="() => secondHasFocus = false" />
          <span v-else-if="state > 1">{{secondNumber}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import { Component } from 'vue-property-decorator'

@Component
export default class Axis extends Vue {
  firstNumber = '' // [6-9]
  secondNumber = '' // [2-8]
  sum = ''

  firstHasFocus = false
  secondHasFocus = false

  firstNumberExpected = '7'
  secondNumberExpected = '5'
  sumExpected = '12'

  fillNumbers() {
    const a = Number(prompt('Введите число a | a ​​∈ [6, 9]')) // eslint-disable-line no-alert
    if (isNaN(a) || a < 6 || a > 9) {
      return false
    }
    const b = Number(prompt('Введите число b | a + b ​∈ [11, 14]')) // eslint-disable-line no-alert
    if (isNaN(b) || (a + b) < 11 || (a + b) > 14) {
      return false
    }
    this.firstNumberExpected = String(a)
    this.secondNumberExpected = String(b)
    this.sumExpected = String(a + b)
    return true
  }

  created() {
    while (!this.fillNumbers());
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
  background: url(../assets/arc.png) no-repeat;
  background-size: 100% 100%;
  margin-top: -80px;
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
