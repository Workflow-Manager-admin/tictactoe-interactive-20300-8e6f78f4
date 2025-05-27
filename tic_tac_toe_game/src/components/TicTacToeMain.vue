<template>
  <div class="ttt-container">
    <h2 class="turn-indicator" :class="{ finished: !!winner || isDraw }">
      <template v-if="winner">
        <span :class="{'accent': winner === 'X', 'secondary': winner === 'O'}">Player {{ winner }}</span> wins!
      </template>
      <template v-else-if="isDraw">
        <span class="draw-message">It's a draw!</span>
      </template>
      <template v-else>
        Turn: <span :class="{'accent': currentPlayer === 'X', 'secondary': currentPlayer === 'O'}">Player {{ currentPlayer }}</span>
      </template>
    </h2>
    <div class="ttt-grid">
      <button
        v-for="(cell, idx) in board"
        :key="idx"
        class="ttt-cell"
        :class="{
          accent: cell === 'X',
          secondary: cell === 'O',
          clickable: !cell && !winner && !isDraw
        }"
        :disabled="!!cell || !!winner || isDraw"
        @click="markCell(idx)"
        aria-label="Grid Cell"
      >
        {{ cell }}
      </button>
    </div>
    <button class="reset-btn" @click="resetGame">
      Restart Game
    </button>
  </div>
</template>

<script setup lang="ts">
// PUBLIC_INTERFACE
import { ref } from 'vue'

/**
 * Main TicTacToe Game Component.
 * Implements a 2-player game with win/draw detection and reset option.
 * Colors: #ffffff (primary), #000000 (secondary), #2196f3 (accent)
 */
const EMPTY_BOARD = Array(9).fill('')

const board = ref([...EMPTY_BOARD])
const currentPlayer = ref('X')
const winner = ref('')
const isDraw = ref(false)

// Winning line indexes
const winLines = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8], // rows
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8], // columns
  [0, 4, 8],
  [2, 4, 6]  // diagonals
]

// PUBLIC_INTERFACE
function markCell(idx) {
  if (board.value[idx] || winner.value || isDraw.value) return
  board.value[idx] = currentPlayer.value
  checkGameState()
  if (!winner.value && !isDraw.value) {
    currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
  }
}

// PUBLIC_INTERFACE
function checkGameState() {
  // Win check
  for (const [a, b, c] of winLines) {
    if (
      board.value[a] &&
      board.value[a] === board.value[b] &&
      board.value[a] === board.value[c]
    ) {
      winner.value = board.value[a]
      return
    }
  }
  // Draw check
  isDraw.value = board.value.every(cell => cell) && !winner.value
}

// PUBLIC_INTERFACE
function resetGame() {
  board.value = [...EMPTY_BOARD]
  currentPlayer.value = 'X'
  winner.value = ''
  isDraw.value = false
}
</script>

<style scoped>
.ttt-container {
  background: #ffffff;
  border-radius: 16px;
  box-shadow: 0 2px 16px 0 rgba(0,0,0,0.05);
  min-width: 325px;
  max-width: 95vw;
  margin: 3rem auto 0;
  padding: 2rem 1.5rem 1.5rem;
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.turn-indicator {
  font-size: 1.3rem;
  margin-bottom: 1.3rem;
  font-weight: bold;
  color: #000;
  min-height: 2.2em;
}
.turn-indicator .accent {
  color: #2196f3;
}
.turn-indicator .secondary {
  color: #000;
}
.turn-indicator.finished {
  color: #2196f3;
}
.draw-message {
  color: #999;
}
.ttt-grid {
  margin: 0 auto 1.65rem;
  display: grid;
  grid-template-columns: repeat(3, 64px);
  grid-template-rows: repeat(3, 64px);
  gap: 0.6rem;
  justify-content: center;
}
.ttt-cell {
  background: #fff;
  border-radius: 13px;
  border: 2px solid #2196f3;
  box-sizing: border-box;
  font-size: 2.45rem;
  font-weight: 600;
  color: #000;
  width: 64px;
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: center;
  outline: none;
  cursor: pointer;
  transition: background 0.22s, box-shadow 0.12s;
  user-select: none;
}
.ttt-cell.accent {
  color: #2196f3;
  border-color: #2196f3;
}
.ttt-cell.secondary {
  color: #000;
  border-color: #000;
}
.ttt-cell:disabled {
  background: #eaeaea;
  color: #bbb;
  cursor: default;
}
.ttt-cell.clickable:hover:not(:disabled) {
  background: #f3fafd;
  box-shadow: 0 0 0 2px #2196f369;
}
.reset-btn {
  margin-top: 0.8rem;
  border-radius: 10px;
  background: #2196f3;
  border: none;
  color: #fff;
  font-size: 1.05rem;
  font-weight: 600;
  padding: 0.6em 1.95em;
  cursor: pointer;
  transition: background 0.16s, color 0.19s;
  box-shadow: 0 1px 4px 0 rgba(0,0,0,0.07);
  letter-spacing: 0.01em;
}
.reset-btn:hover,
.reset-btn:focus {
  background: #1270be;
  color: #fff;
  outline: none;
}
@media (max-width: 440px) {
  .ttt-grid {
    grid-template-columns: repeat(3, 44px);
    grid-template-rows: repeat(3, 44px);
    gap: 0.34rem;
  }
  .ttt-cell {
    font-size: 1.5rem;
    width: 44px;
    height: 44px;
  }
}
</style>
