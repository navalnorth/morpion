<template>
  <div>
    <h1>Le jeu du morpion</h1>
    <div class="divDivButton">
      <div class="divButton">
        <button v-for="(button, index) in buttons" :key="index" :class="['button', button.isActive]"
          @click="handleClick(index)" :disabled="button.isDisabled">
        </button>
      </div>
    </div>
    <div v-if="winner" class="winnerMessage">{{ winnerMessage }}</div>
  </div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';

const buttons = ref(
  Array.from({ length: 9 }, () => ({ isActive: '', isDisabled: false })),
);

const currentPlayer = ref('X');
const winner = ref<string | null>(null);

const checkWinner = () => {
  const winnPatterns = [
    [0, 1, 2], [3, 4, 5], [6, 7, 8],
    [0, 3, 6], [1, 4, 7], [2, 5, 8],
    [0, 4, 8], [2, 4, 6],
  ];
  return winnPatterns.some((pattern) => pattern.every((index) => buttons.value[index].isActive === `bg${currentPlayer.value}`));
};

const handleClick = (index: number) => {
  if (buttons.value[index].isDisabled || winner.value) return;

  buttons.value[index].isActive = currentPlayer.value === 'X' ? 'bgX' : 'bgO';
  buttons.value[index].isDisabled = true;

  if (checkWinner()) {
    winner.value = currentPlayer.value;
  } else if (buttons.value.every((button) => button.isDisabled)) {
    winner.value = 'draw';
  } else {
    currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X';
  }
};

const winnerMessage = computed(() => {
  if (winner.value === 'draw') return 'Match nul !';
  if (winner.value) return `Le ${winner.value} a gagné`;
  return '';
});
</script>
<style scoped lang="scss">
div {
  h1 {
    text-align: center;
  }
  .divDivButton {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 50vh;
    .divButton {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      justify-content: center;
      align-items: center;
      position: absolute;
    }
    .button {
      padding: 40px;
      font-size: 24px;
    }
    .bgX {
      background: url('./assets/X.png') no-repeat center;
      background-size: cover;
    }
    .bgO {
      background: url('./assets/O.png') no-repeat center;
      background-size: cover;
    }
  }
  .winnerMessage {
    text-align: center;
    margin-top: 20px;
    font-size: 24px;
    color: #28a745;
  }
}
</style>
