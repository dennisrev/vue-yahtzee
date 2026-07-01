<script setup>
import ThrownDice from './components/ThrownDice.vue'
import ScoreTable from './components/ScoreTable.vue'

import { ref } from 'vue';

const dice = ref([1,2,3,4,5]);

</script>

<template>
  
  <h2>Yahtzee in Vue</h2>

  <ThrownDice v-model="dice"/>
  <ScoreTable v-model="dice"/>
  
  
</template>
