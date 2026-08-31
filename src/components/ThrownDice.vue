<script setup lang="ts">

import { ref, watch } from 'vue';

const dice = defineModel<number[]>({ default: () => [] });

const ROLLS = 5;
const MAX_WORPEN = 3;

const worpTeller = ref(0);

const heldDice = ref<boolean[]>([false, false, false, false, false]);

function rollDices() {
  if (worpTeller.value >= MAX_WORPEN) return;
  if (worpTeller.value === 0) {
    dice.value.length = 0;
    heldDice.value = [false, false, false, false, false]; 

    for (let i = 0; i < ROLLS; i++) {
      const roll = 1 + Math.floor(Math.random() * 6);
      dice.value.push(roll);
    }
  } else {
    for (let i = 0; i < ROLLS; i++) {
      if (!heldDice.value[i]) {
        const roll = 1 + Math.floor(Math.random() * 6);
        dice.value[i] = roll;
      }
    }
  }
  worpTeller.value++;
};

function toggleHold(index: number) {
  if (worpTeller.value > 0 && worpTeller.value < MAX_WORPEN) {
    heldDice.value[index] = !heldDice.value[index];
  }
}

watch(dice, (nieuweDice) => {
  if (nieuweDice.length === 0) {
    worpTeller.value = 0;
    heldDice.value = [false, false, false, false, false];
  }
});

</script>

<template>

<div align="center">
  <p v-if="worpTeller > 0">
    Worp: <strong>{{ worpTeller }} / {{ MAX_WORPEN }}</strong>
    <span v-if="worpTeller === MAX_WORPEN">
      ⚠️ Geen worpen meer over. Kies een vakje op je scorekaart!
    </span>
    <span v-else>
      <br>💡 Klik op een dobbelsteen om deze vast te zetten.
    </span>
  </p>
  <p v-else>
    Druk op gooien om je beurt te beginnen.
  </p>

  <button 
    @click="rollDices"
    :disabled="worpTeller >= MAX_WORPEN"
  >
    {{ worpTeller === 0 ? 'Start Beurt (Gooien!)' : 'Opnieuw Gooien'}}
  </button>

  <table v-if="dice.length === 5" align="center">
    <thead>
      <tr>
        <th colspan="5">Gegooide dobbelstenen:</th>
      </tr>
        
    </thead>
    <tbody>
      <tr>
        <td 
          v-for="(diceValue, index) in dice"
          :key="index"
          @click="toggleHold(index)"
          :class="['steen', { 'is-held': heldDice[index] }]"
          > 
            {{ diceValue }} 
            <div v-if="heldDice[index]" class="hold-badge">VAST</div>
          </td>
      </tr>
    </tbody>
  </table>

</div>

</template>

<style scoped>
.werper-sectie { font-family: sans-serif; margin-bottom: 25px; }

.rol-btn {
  background-color: #48bb78; color: white; border: none; padding: 10px 20px;
  font-size: 1em; border-radius: 4px; cursor: pointer; font-weight: bold; margin-bottom: 15px;
}
.rol-btn:disabled { background-color: #cbd5e0; cursor: not-allowed; }

.dobbel-tabel { border-collapse: collapse; margin-top: 15px; }
th { background-color: #4a5568; color: white; padding: 8px 20px; }

.steen {
  font-size: 1.6em; font-weight: bold; text-align: center;
  background-color: white; border: 2px solid #cbd5e0;
  width: 65px; height: 65px; border-radius: 8px;
  cursor: pointer; position: relative;
  transition: all 0.2s ease;
  user-select: none;
}
.steen:hover { background-color: #f7fafc; }

.steen.is-held {
  background-color: #ebf8ff;
  border-color: #3182ce;
  color: #2b6cb0;
  transform: translateY(-2px);
}

.hold-badge {
  font-size: 9px;
  background-color: #3182ce;
  color: white;
  position: absolute;
  bottom: 3px;
  left: 50%;
  transform: translateX(-50%);
  padding: 1px 4px;
  border-radius: 3px;
  letter-spacing: 0.5px;
}

</style>