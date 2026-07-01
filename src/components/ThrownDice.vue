<script setup>

import { defineModel } from 'vue';

const dice = defineModel();

const ROLLS = 5;

function rollDices() {
  dice.value.length = 0;
  for (let i=0; i < ROLLS; i++) {
    const roll = 1 + Math.floor(Math.random() * 6);
    dice.value.push(roll);
  };
};

</script>

<template>

<div>
    <button @click="rollDices">Gooien!</button>

    <table align="center">
      <thead colsspan>
        <tr>
          <td colspan="5">De vijf gegooide dobbelstenen:</td>
        </tr>
        
      </thead>
      <tbody>
        <tr>
          <td v-for="(diceValue, index) in dice" :key="index"> {{ diceValue }} </td>
        </tr>
      </tbody>
    </table>

  </div>

</template>