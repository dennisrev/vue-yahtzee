<script setup lang="ts">
import ThrownDice from './components/ThrownDice.vue'
import ScoreTable from './components/ScoreTable.vue'

import { ref } from 'vue';

const ROLLS = 5;

type Dice = [number, ...number[]] & { length: typeof ROLLS };

const dice = ref<number[]>([]);

const rolledDice = ref<Dice>([1,2,3,4,5]);

function rollDices() {
  dice.value.length = 0;
  for (let i=0; i < ROLLS; i++) {
    const roll = 1 + Math.floor(Math.random() * 6);
    dice.value.push(roll);
  };
  rolledDice.value = [...dice.value] as Dice;
};

</script>

<template>
  
  <h2>Yahtzee in Vue</h2>

  <button @click="rollDices">Gooien!</button>
  <ThrownDice v-model="rolledDice"/>
  <ScoreTable v-model="rolledDice"/>
  
  
</template>
