<script setup lang="ts">
import ThrownDice from './components/ThrownDice.vue'
import ScoreTable from './components/ScoreTable.vue'

import { ref } from 'vue';

const dice = ref<number[]>([]);

</script>

<template>
  
  <h2>Yahtzee in Vue</h2>

  <div>
    
  </div>
  <ThrownDice v-model="dice"/>
  <ScoreTable v-model="dice"/>
  
</template>
