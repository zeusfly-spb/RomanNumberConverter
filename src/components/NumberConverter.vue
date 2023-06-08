<template>
  <div>
    <div  class="card">
      <label for="string-input">Римские:
        <input
          type="text"
          id="string-input"
          v-model="strNumber"
          @input="filterStrNumber(); romanToInt()"
        >
      </label>
      &nbsp;&nbsp;&nbsp;
      <label for="number-input">Десятичные:
        <input
          type="number"
          min="0"
          id="number-input"
          v-model.number="intNumber"
          @input="intNumber[0] === 0 ? intNumber = intNumber.substring(1) : null; intToRoman();"
        >
      </label>
    </div>
  </div>
</template>

<script setup>
/* eslint-disable no-undef,no-unused-vars */
import { ref, reactive, computed, onMounted, nextTick } from "vue";

const strNumber = ref('');
const intNumber = ref(0);

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

function romanToInt() {
  const digits = JSON.parse(JSON.stringify(signs));
  let result = 0;
  const str = strNumber.value;
  if (!str.length) {
    intNumber.value = result;
  }
  for (let i = str.length; i >= 1; i--) {
    const sign = str[i - 1];
    digits[sign].present = true;
    const current = digits[sign];
    const keysAbove = allDigits.value.filter(key => digits[key].value > current.value);
    if (keysAbove.length) {
      for(let j = 0; j < keysAbove.length; j++) {
        if (digits[keysAbove[j]].present) {
          current.value = - current.value;
          break;
        }
      }
    }
    result += current.value;
  }
  intNumber.value = result;
}

function intToRoman() {
    let digits = JSON.parse(JSON.stringify(signs));
    let n = intNumber.value;
    let result = '';
    allDigits.value.reverse().forEach(key => {
      while (n >= digits[key].value) {
        result += key;
        n -= digits[key].value;
      }
    })
  try{
    allDigits.value.forEach(key => {
      const keysAbove = allDigits.value.reverse().filter(item => digits[item].value > digits[key].value);
      if (keysAbove.length) {
        const upperKey = keysAbove[0] || null;
        const higherKey = keysAbove[1] || null;
        if (upperKey) {
          result = result.replace(key.repeat(4), `${key}${upperKey}`);
        }
        if (upperKey && higherKey) {
          result = result.replace(`${upperKey}${key}${upperKey}`, `${key}${higherKey}`);
        }
      }
    });
    strNumber.value = result;
  } catch (e) {
    console.log(e.message)
  }
}
</script>

<style lang="css" scoped>
.card {
  padding-left: 3em!important;
}
</style>