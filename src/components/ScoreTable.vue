<script setup lang="ts">

import { ref, computed } from 'vue';

const dice = defineModel<number[]>({ default: () => [] });

const scoreBlok = ref<Record<string, number | null>>({
    'Enen': null, 'Tweeën': null, 'Drieën': null, 'Vieren': null, 'Vijven': null, 'Zessen': null,
    'Three of a Kind': null, 'Four of a Kind': null, 'Full House': null,
    'Kleine Straat': null, 'Grote Straat': null, 'Kans': null, 'Yahtzee': null
});

const count = computed(() => {
   const countHelp = Array(6).fill(0);
   for (let diceNumber in dice.value) {
       countHelp[dice.value[diceNumber]-1]++;
   };
   return countHelp;
});

const sumDice = computed(() => dice.value.reduce((sum, value) => sum + value, 0));

const isXOfAKind = (xSameDices: number) => count.value.some(cnt => cnt >= xSameDices);

const threeOfAKind = computed(() => isXOfAKind(3) ? sumDice.value : 0);
const fourOfAKind = computed(() =>  isXOfAKind(4) ? sumDice.value : 0);
const yahtzee = computed(() => isXOfAKind(5) ? 50 : 0);
const fullHouse = computed(() => count.value.includes(3) && count.value.includes(2) ? 25 : 0)

const BIGSTREETS = ["12345", "23456"];
const SMALLSTREETS = ["1234","12346", "2345", "3456", "13456"].concat(BIGSTREETS);
const uniqueDice = computed(() => [...new Set(dice.value)].sort());

const smallStreet = computed(() => SMALLSTREETS.includes(uniqueDice.value.join("")) ? 30 : 0);
const bigStreet = computed(() => BIGSTREETS.includes(uniqueDice.value.join("")) ? 40 : 0);

const alleScores = computed(() => {
    if (!dice.value || dice.value.length !== 5) return {};
    
    const namenBovenkantOpties = ['Enen', 'Tweeën', 'Drieën', 'Vieren', 'Vijven', 'Zessen'];
    const bovenkantScores = Object.fromEntries(namenBovenkantOpties.map( (n,i) => [n, count.value[i] * (i + 1)]));

    return {
        ...bovenkantScores,
        "Three of a Kind": threeOfAKind.value,
        "Four of a Kind": fourOfAKind.value,
        "Full House": fullHouse.value,
        "Kleine Straat": smallStreet.value,
        "Grote Straat": bigStreet.value,
        "Kans": sumDice.value,
        "Yahtzee": yahtzee.value
    };
})

const newScore = computed(() => {
    const scoreEntries = Object.entries(alleScores.value) as [string, number][];
    if (scoreEntries.length === 0) return { categorie: "Geen", punten: 0 };

    const openOpties = scoreEntries.filter(([categorie]) => scoreBlok.value[categorie] === null);
    if (openOpties.length === 0) return { categorie: "Vol!", punten: 0};

    return openOpties.reduce((max, [categorie, punten]: [string, number]) => {
        return punten > max.punten ? { categorie, punten} : max
    }, { categorie: "", punten: -1 });
});

const selecteerScore = (categorie: string, punten: number) => {
    if (scoreBlok.value[categorie] === null) {
        scoreBlok.value[categorie] = punten;
        dice.value = [];
    }
};

const subTotaalBoven = computed(() => {
    const bovenkantNamen = ['Enen', 'Tweeën', 'Drieën', 'Vieren', 'Vijven', 'Zessen'];
    return bovenkantNamen.reduce((som, naam) => som + (scoreBlok.value[naam] || 0), 0);
});
const bonusPunten = computed(() => {
    return subTotaalBoven.value >= 63 ? 35 : 0;
});
const totalePunten = computed(() => {
    const alleVakjes = Object.values(scoreBlok.value).reduce((totaal: number, punten) => totaal + (punten || 0), 0);
    return alleVakjes + bonusPunten.value;
});

const resetSpel = () => {
    for (const categorie in scoreBlok.value) {
        scoreBlok.value[categorie] = null;
    }
    dice.value = [];
}

const isSpelAfgelopen = computed(() => {
    return Object.values(scoreBlok.value).every(punten => punten !== null); 
});

</script>

<template>

  <div v-if="dice && dice.length ===5">

        <table align="center">
            <thead>
                <tr>
                    <th colspan="3">Tabel met alle mogelijke Yahtzee scores</th>
                </tr>
                <tr>
                    <th>Combinatie</th>  
                    <th>Punten</th>  
                    <th>Actie</th>  
                </tr>
            </thead>
            <tbody>
                <tr
                    v-for="(punten, categorie) in alleScores"
                    :key="categorie"
                >
                    <td>{{ categorie }}</td>
                    <td>{{ punten }}</td>
                    <td>
                        <button
                            v-if="scoreBlok[categorie] === null"
                            @click="selecteerScore(categorie, punten)"
                        >
                            {{ categorie === newScore.categorie ? '🔥 Kiezen' : 'Kiezen' }}
                        </button>
                        <span v-else>Ingevuld</span>
                    </td>
                </tr>
            </tbody>
        </table>
  </div>

  <div>
        <h3>📋 Scorekaart </h3>

        <table align="center">
            <thead>
                <tr>
                    <th>Combinatie</th>
                    <th>Vastgezette score</th>
                </tr>
            </thead>
            <tbody>
                <tr
                    v-for="(vastgezettePunten, categorie) in scoreBlok"
                    :key="'blok-' + categorie"
                >
                    <td>{{ categorie }}</td>
                    <td>
                        <strong>{{ vastgezettePunten !== null ? vastgezettePunten : '-' }}</strong>
                    </td>
                </tr>

                <tr>
                    <td><em>Subtotaal Bovenkant</em></td>
                    <td><strong>{{ subTotaalBoven }} / 63</strong></td>
                </tr>
                <tr>
                    <td><em>Bonus (bij min. 63 pnt)</em></td>
                    <td><strong>+{{ bonusPunten }} punten</strong></td>
                </tr>

                <tr>
                    <td><strong>TOTAALSCORE (Inclusief Bonus)</strong></td>
                    <td><strong>{{ totalePunten }} punten</strong></td>
                </tr>
            </tbody>
        </table>
        <div align="center">
            <button @click="resetSpel">
                {{ isSpelAfgelopen ? '🎉 Spel afgelopen! Speel opnieuw' : 'Nieuw Spel'}}
            </button>
        </div>
  </div>
</template>