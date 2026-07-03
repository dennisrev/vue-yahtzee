<script setup>

import { computed, defineModel } from 'vue';

const dice = defineModel();

const count = computed(() => {
   const countHelp = Array(6).fill(0);
   for (let diceNumber in dice.value) {
       countHelp[dice.value[diceNumber]-1]++;
   };
   return countHelp;
});

const isXOfAKind = xSameDices => count.value.some(cnt => cnt >= xSameDices);

const threeOfAKind = computed(() => isXOfAKind(3) ? sumDice : 0);

const fourOfAKind = computed(() =>  isXOfAKind(4) ? sumDice : 0);

const yahtzee = computed(() => isXOfAKind(5) ? 50 : 0);

const fullHouse = computed(() => count.value.includes(3) && count.value.includes(2) ? 25 : 0)

const BIGSTREETS = ["12345", "23456"];
const SMALLSTREETS = ["1234","12346", "2345", "3456", "13456"].concat(BIGSTREETS);
const uniqueDice = computed(() => [...new Set(dice.value)].sort());

const smallStreet = computed(() => SMALLSTREETS.includes(uniqueDice.value.join("")) ? 30 : 0);
const bigStreet = computed(() => BIGSTREETS.includes(uniqueDice.value.join("")) ? 40 : 0);

const sumDice = computed(() => dice.value.reduce((sum, value) => sum + value, 0));

</script>

<template>

<div>

    <table align="center">
      <thead>
        Tabel met all mogelijke Yahtzee-scores:
      </thead>
      <tbody>
        <tr>
            <th>Combinatie</th>
            <th>Punten</th>
        </tr>
        <tr v-for="(countValue, index) in count" :key="index">
            <td> {{ index + 1 }}</td>
            <td> {{ countValue * (index + 1) }}</td>
        </tr>
        <tr>
            <td>Drie gelijke</td>
            <td> {{ threeOfAKind }}</td>
        </tr>
        <tr>
            <td>Vier gelijke</td>
            <td> {{ fourOfAKind }}</td>
        </tr>
        <tr>
            <td>Kleine Straat</td>
            <td> {{ smallStreet }}</td>
        </tr>
        <tr>
            <td>Grote Straat</td>
            <td> {{ bigStreet }}</td>
        </tr>
        <tr>
            <td>FullHouse</td>
            <td> {{ fullHouse }}</td>
        </tr>
        <tr>
            <td>Kans</td>
            <td> {{ sumDice }}</td>
        </tr>
        <tr>
            <td>Yahtzee</td>
            <td> {{ yahtzee }}</td>
        </tr>
      </tbody>
    </table>

  </div>

</template>