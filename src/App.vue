<template>
  <h1>Tic Tac Toe</h1>
  <p>Current player: {{ currentPlayer.symbol }}</p>
  <p>Score: player1: {{ player1.score }} player2: {{ player2.score }}</p>

  <template v-if="isFieldFull">
    <div>
      <p>Player {{ currentPlayer.key }} win!</p>
      <button @click="resetGame">Reset</button>
    </div>
  </template>

  <ul class="field">
    <li v-for="(row, R) in field" :key="R">
      <ul>
        <li
          v-for="(column, C) in row"
          :key="C"
          class="field-item"
          @click="handleItemClick(R, C)"
        >
          <span class="symbol">
            {{ field[R][C] }}
          </span>
        </li>
      </ul>
    </li>
  </ul>
</template>

<script setup>
import { reactive, ref } from "vue";

const player1 = {
  key: "player1",
  symbol: "X",
  score: 0,
  moveCount: 0,
};

const player2 = {
  key: "player2",
  symbol: "O",
  score: 0,
  moveCount: 0,
};

let currentPlayer = player1;
let isFieldFull = ref(false);

const field = reactive([
  [null, null, null],
  [null, null, null],
  [null, null, null],
]);

function handleItemClick(row, column) {
  if (field[row][column] !== null || isFieldFull.value) {
    return;
  }

  field[row][column] = currentPlayer.symbol;
  currentPlayer.moveCount += 1;

  const result = checkResult(row, column);

  if (result) {
    currentPlayer.score += 1;
    isFieldFull.value = true;
    return;
  } else {
    changePlayer();
  }
}

function changePlayer() {
  if (currentPlayer.key === player1.key) {
    currentPlayer = player2;
  } else {
    currentPlayer = player1;
  }
}

function checkResult(row, column) {
  let result = false;

  if (currentPlayer.moveCount < 3) {
    return result;
  }

  if (row === column || row - column === 2 || row - column === -2) {
    // check for
    // [x, -, x]
    // [-, x, -]
    // [x, -, x]
    result =
      checkByRow(row, column) ||
      checkByColumn(row, column) ||
      checkByDiagonal() ||
      checkByRevertDiagonal();
  } else {
    // check for
    // [-, x, -]
    // [x, -, x]
    // [-, x, -]
    result = checkByRow(row, column) || checkByColumn(row, column);
  }

  return result;
}

function checkByRow(row, column) {
  // row = 0
  // column = 1
  // [x, x, x]
  // [-, -, -]
  // [-, -, -]
  return field[row].every((item) => item === currentPlayer.symbol);
}
function checkByColumn(row, column) {
  // row = 0
  // column = 1
  // [-, x, -]
  // [-, x, -]
  // [-, x, -]

  const columnItems = field.map((row) => row[column]);
  return columnItems.every((item) => item === currentPlayer.symbol);
}
function checkByDiagonal() {
  // row = 1
  // column = 1
  // [x, -, -]
  // [-, x, -]
  // [-, -, x]

  const diagonalItems = field.map((row, index) => row[index]);
  return diagonalItems.every((item) => item === currentPlayer.symbol);
}

function checkByRevertDiagonal() {
  // row = 1
  // column = 1
  // [-, -, x]
  // [-, x, -]
  // [x, -, -]

  const diagonalItems = field.map((row, index) => row[row.length - 1 - index]);
  return diagonalItems.every((item) => item === currentPlayer.symbol);
}

function resetGame() {
  field.forEach((row, R) => {
    row.forEach((column, C) => {
      field[R][C] = null;
    });
  });

  player1.moveCount = 0;
  player2.moveCount = 0;
  isFieldFull.value = false;

  currentPlayer = player1;
}
</script>

<style scoped>
* {
  color: white;
}
.field {
  display: flex;
}
.field-item {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100px;
  height: 100px;
  border: 1px solid white;
}

h1 {
  font-s: 20px;
}

button {
  margin: 20px 0;
  padding: 10px;
  border: 1px solid white;
  background: transparent;
}
</style>
