<script setup>

import { computed, defineModel } from 'vue';

const dice = defineModel();

const count = computed(() => {
    const countHelp = Array(6).fill(0);
    for (let key in dice.value) {
        countHelp[dice.value[key]-1]++;
    };
    return countHelp;
});

const isXOfAKind = computed(() => {
    const isXOfAKindHelp = Array(3).fill(false);
    for (let x = 3; x <= 5; x++) {
        isXOfAKindHelp[x-3] = count.value.some(cnt => cnt >=x);
    };
    return isXOfAKindHelp;
})

const isFullHouse = computed(() => {
    return count.value.includes(3) && count.value.includes(2);
});

const isBigStreet = computed(() => {

    const sortedDice = dice.value.sort(); 
    const uniqueDice = [];
    sortedDice.forEach( (diceValue) => {
        if ( uniqueDice.indexOf(diceValue) == -1 ) {
            uniqueDice.push(diceValue);
        }
    });
    const streetLength = 0;
    const maxStreetLength = 0;
    for (let i = 1; i <= uniqueDice.length; i++) {
        if (uniqueDice[i] - uniqueDice[i-1] == 1 ){
            streetLength++;
        } else {
            maxStreetLength = streetLength;
            streetLength = 0;
        };
    }
    return maxStreetLength == 5;

});

const sumDice = computed(() => {
    return dice.value.reduce(getSum);
});

function getSum(sum, value) { 
    return sum + value;
};

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
            <td>Eenen</td>
            <td> {{ count[0] }}</td>
        </tr>
        <tr>
            <td>Tweeën</td>
            <td> {{ count[1] * 2 }}</td>
        </tr>
        <tr>
            <td>Drieën</td>
            <td> {{ count[2] * 3 }}</td>
        </tr>
        <tr>
            <td>Vieren</td>
            <td> {{ count[3] * 4 }}</td>
        </tr>
        <tr>
            <td>Vijfen</td>
            <td> {{ count[4] * 5 }}</td>
        </tr>
        <tr>
            <td>Zessen</td>
            <td> {{ count[5] * 6 }}</td>
        </tr>
        <tr>
            <td>Drie gelijke</td>
            <td> {{ isXOfAKind[0] ? sumDice : 0 }}</td>
        </tr>
        <tr>
            <td>Vier gelijke</td>
            <td> {{ isXOfAKind[1] ? sumDice : 0 }}</td>
        </tr>
        <tr>
            <td>Kleine Straat</td>
            <td> {{ 0 }}</td>
        </tr>
        <tr>
            <td>Grote Straat</td>
            <td> {{ isBigStreet ? 40 : 0 }}</td>
        </tr>
        <tr>
            <td>FullHouse</td>
            <td> {{ isFullHouse ? 25 : 0 }}</td>
        </tr>
        <tr>
            <td>Kans</td>
            <td> {{ sumDice }}</td>
        </tr>
        <tr>
            <td>Yahtzee</td>
            <td> {{ isXOfAKind[2] ? 50 : 0 }}</td>
        </tr>
      </tbody>
    </table>

  </div>

</template>