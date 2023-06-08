<template>
  <div>
    <div  class="card">
      <label for="string-input">Римские:
        <input
          type="text"
          name="string-input"
          v-model="strNumber"
          @input="filterStrNumber"
        >
      </label>
      &nbsp;&nbsp;&nbsp;
      <label for="number-input">Десятичные:
        <input
          type="text"
          name="number-input"
          v-model.number="intNumber"
        >
      </label>
      &nbsp;&nbsp;&nbsp;
      <button
        @click="romanToInt"
      >
        Считать
      </button>
    </div>
  </div>
</template>

<script setup>
/* eslint-disable no-undef,no-unused-vars */
import { ref, reactive, computed, onMounted, nextTick } from "vue";

const numbers = reactive({
  intNumber: 0,
  strNumber: ''
});

const signs = reactive({
  I: { present: false, value: 1 },
  V: { present: false, value: 5 },
  X: { present: false, value: 10 },
  L: { present: false, value: 50 },
  C: { present: false, value: 100 },
  D: { present: false, value: 500 },
  M: { present: false, value: 1000 },
});

const allDigits = computed(() => Object.keys(signs));

function filterStrNumber() {
  strNumber.value = strNumber.value.toUpperCase().split('')
    .filter(key => allDigits.value.includes(key)).join('')
}

const intNumber = computed({
  get() {
    return numbers.intNumber;
  },
  set(val) {
    numbers.intNumber = val;
  }
});

const strNumber = computed({
  get() {
    return numbers.strNumber;
  },
  set(val) {
    numbers.strNumber = val;
  }
});

function validateInput(event) {
  const inputChar = String.fromCharCode(event.keyCode).toUpperCase();
  if (!allDigits.value.includes(inputChar)) {
    event.preventDefault();
  } else {
    strNumber.value += inputChar;
  }
}

function romanToInt() {
  let result = 0;
  const str = strNumber.value;
  if (!str.length) {
    return result;
  }
  for (let i = str.length; i >= 1; i--) {
    try {
      const sign = str[i - 1];
      signs[sign]['present'] = true;
      const current = signs[sign];
      result += current['amount'];
    } catch (e) {
      console.log("Error", e.stack);
      console.log("Error", e.name);
      console.log("Error", e.message);
    }
  }
  intNumber.value = result;
}

</script>

<style lang="css" scoped>
.card {
  padding-left: 3em!important;
}
</style>