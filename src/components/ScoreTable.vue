<script setup>

import { computed, defineModel } from 'vue';

const dice = defineModel();

console.log(dice.value);


const amounts = computed(() => {
    let count = [0,0,0,0,0,0];
    for (let i = 0; i < dice.value.length; i++) {
        count[dice.value[i]-1]++;
    }
    return count;
});

const numbers = computed( (x) => {
    return count.value[x+1] * x;
});

console.log(amounts.value);


const sumDice = computed(() => {
    return dice.value.reduce(getSum);
});

function getSum(sum, value) { 
    return sum + value;
};

//console.log(dice.value[0]);

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
        <tr>
            <td>Enen</td>
            <td> {{ amounts[0] }}</td>
        </tr>
        <tr>
            <td>Enen</td>
            <td> {{ numbers[1] }}</td>
        </tr>
        <tr>
            <td>Kans</td>
            <td> {{ sumDice }}</td>
        </tr>
      </tbody>
    </table>

  </div>

</template>