<script setup>

import { defineModel } from 'vue';

const dice = defineModel();

const rolls = 5;

function rollDices() {
    for (let i=0; i < rolls; i++) {
        dice.value[i] = 1 + Math.floor(Math.random() * 6);
    }
}

</script>

<template>

<div>
    <button @click="rollDices">Gooien!</button>

    <table align="center">
      <thead>
        De vijf gegooide dobbelstenen:
      </thead>
      <tbody>
        <tr>
          {{dice[0] }} {{ dice[1] }} {{ dice[2] }} {{ dice[3] }} {{ dice[4] }} 
        </tr>
      </tbody>
    </table>

  </div>

</template>