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

// functie die in computeds wordt herhaald 

//const xOfAKind = (x) => {
//
//}

//const threeOfAkind = computed(() => adsdasdas(3) ? sumf : 0)
//const threeOfAkind = computed(() => adsdasdas(4) ? sumf : 0)
//const threeOfAkind = computed(() => adsdasdas(5) ? sumf : 0)

const isXOfAKind = computed(() => {
    const xSameDices = [];
    for (let xSame = 3; xSame <= 5; xSame++) {
        xSameDices.push(count.value.some(cnt => cnt >=xSame));  
    };
    return xSameDices;
});

const isFullHouse = computed(() => {
    return count.value.includes(3) && count.value.includes(2);
});

const isStreet = computed(() => {
    const streets = [];

    const uniqueDice = [...new Set(Object.values(dice.value))];
    const diceLength = uniqueDice.length;
    
    if ( ( diceLength == 4 && uniqueDice[diceLength-1] - uniqueDice[0] == 3 ) || 
             ( diceLength == 5 && ( uniqueDice[1] == 3 || uniqueDice[diceLength-2] == 4 )  ) ) {
        streets.push(true);
    } else {
        streets.push(false);
    }
    if ( diceLength == 5 && uniqueDice[diceLength-1] - uniqueDice[0] == 4 ) {
        streets.push(true);
    } else {
        streets.push(false);
    }
    return streets;
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
        <tr v-for="(countValue, index) in count" :key="index">
            <td> {{ index + 1 }}</td>
            <td> {{ countValue * (index+1) }}</td>
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
            <td> {{ isStreet[0] ? 30 : 0 }}</td>
        </tr>
        <tr>
            <td>Grote Straat</td>
            <td> {{ isStreet[1] ? 40 : 0 }}</td>
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