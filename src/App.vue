<script setup lang="ts">

import { ref } from 'vue';
import axios from 'axios';


const valueToConvert = ref(0);
const selected = ref('RUB');
const convertedCurrency = ref('USD');

const convertedValue = ref(0);
const currencies = [
  {
  id: 1,
  value: 'RUB',
  },{
    id: 2,
    value: 'USD',
  },{
    id: 3,
    value: 'EUR'
  }
];

const error_message = ref('');

function onChange(event: any) {
  
  selected.value = event.target.value;
  console.log(selected.value);
}

async function convert() {
  try {
    const req = await axios.get(`https://v6.exchangerate-api.com/v6/2a5457f6bab903ccca861954/latest/${selected.value}`);
    convertedValue.value = req.data.conversion_rates[convertedCurrency.value] * valueToConvert.value;
    convertedValue.value = Number(convertedValue.value.toFixed(2));
    console.log(convertedValue.value);
  }catch(e: any) {
    error_message.value = 'Произошла ошибка. Повторите попытку позже.'
  }
  
}

</script>

<template>
  <header class="header">
    <h1 class="header__title">Конвертер валют</h1>
  </header>

  <main>
    <div class="converter">
      <div class="converter__inner">
        <div class="converter__to-convert grid">
          <span class="converter__text">Введите значение</span>
          <input type="number" v-model="valueToConvert">
          <select @change="onChange" name="currency" id="currency_to_convert" class="select" v-model="selected">
            <option v-for="currency in currencies" :key="currency.id" 
            :value="currency.value" class="option">
            {{ currency.value }}
          </option>
          </select>
        </div>
        <div class="converter__converted grid">
          <span class="converter__text">Полученное значение</span>
          <span class="converter__converted-value">{{ convertedValue }}</span>
          <select name="currency" id="currency_to_convert" class="select" v-model="convertedCurrency">
            <option v-for="currency in currencies" :key="currency.id" 
            :value="currency.value" class="option">
            {{ currency.value }}
          </option>
          </select>
        </div>
        <p v-if="error_message" class="error-message">{{ error_message }}</p>
        <button @click="convert" class="converter__submit-button">
          Конвертировать
        </button>
      </div>
    </div>
  </main>
</template>

<style scoped lang="sass">
.header
  &__title
    text-align: center
.converter
  height: 100%
  display: grid
  place-items: center
  
  &__text
    position: absolute
    bottom: 100%
  &__inner
    display: grid
    place-items: center
    gap: 30px
    @media screen and (min-width: 600px)
      grid-template-columns: 1fr 1fr
      justify-content: center

  &__converted
    width: 100%
  &__converted-value
    height: 100%
    padding: 5px
    font-size: 25px
    color: #000
    background-color: #fff
  &__submit-button
    padding: 5px 40px
    height: 40px
    font-size: 18px
    width: fit-content
    background-color: #3fe06a
    color: #fff
    font-weight: 600
    text-transform: uppercase
    @media screen and (min-width: 600px)
      grid-column: 1/3
.grid
  position: relative
  display: grid
  grid-template-columns: 2fr 1fr
  grid-template-rows: 40px
  column-gap: 8px

.error-message
  color: #e32938
</style>
