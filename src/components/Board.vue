<script setup>
  import { ref, computed } from 'vue'
  import Square from './Square.vue'

  const STATE_PLAY = 0
  const STATE_WIN = 1
  const STATE_DRAW = 2
  const PLAYER_0 = 0
  const PLAYER_1 = 1
  const BOARD_SIZE = 9

  const STATE = [
    "Next Player: ",  // STATE_PLAY (0)
    "Winner: ",       // STATE_WIN (1)
    "Draw"            // STATE_DRAW (2)
  ]

  const PLAYER = [
    "X",              // PLAYER_0 (0)
    "O"               // PLAYER_1 (1)
  ]

  // state
  const state = ref(STATE_PLAY)
  const squares = ref(Array(BOARD_SIZE).fill(null))

  const _winner = ref(null)
  const _line = ref(null)

  const _statusString = computed(() => {
    if (_winner.value) {
      return STATE[state.value] + _winner.value
    }
    else if (isDraw(squares.value)) {
      return STATE[state.value]
    }
    else {
      return STATE[state.value] + nextPlayer(squares.value)
    }
  })

  //
  function reset() {
    squares.fill(null)

  }

  //
  function countEmpty(squares) {
    let c = 0
    for (let i = 0; i < BOARD_SIZE; i++) {
      if (squares[i] === null) {
        c++
      }
    }
    return c;
  }

  //
  function countFilled(squares) {
    return BOARD_SIZE - countEmpty(squares)
  }

  //
  function isDraw(squares) {
    return !countEmpty(squares)
  }

  //
  function nextPlayer(squares) {
    return PLAYER[countFilled(squares) % 2]
  }

  //
  function calculateWinner(squares) {
    const lines = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];

    let winner = null;
    let line = null;

    // test for winner
    for (let i = 0; i < lines.length; i++) {
      const [a, b, c] = lines[i];
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
        // winner
        winner = squares[a]
        line = lines[i]
      }
    }

    return { winner , line }
  }

  //
  function handleClick(squareIndex) {
    console.log('handleClick: ', squareIndex)
    if (squares.value[squareIndex]) {
      console.log('Ignore Click (square filled): ', squareIndex)
      return
    }

    if (state.value !== STATE_PLAY) {
      console.log('Ignore Click (game stopped): ', squareIndex)
      return
    }

    // new move
    console.log('New Move: ', squareIndex)
    const player = nextPlayer(squares.value)
    squares.value[squareIndex] = player

    const { winner, line } = calculateWinner(squares.value)
    _winner.value = winner
    _line.value = line

    if (_winner.value) {
      state.value = STATE_WIN
    }
    else if (isDraw(squares.value)) {
      state.value = STATE_DRAW
    }
    else {
      state.value = STATE_PLAY
    }
 }

</script>

<template>
  <h2>Board</h2>
  <div>
    <div class="row">
      <Square index=0 v-on:squareClick="handleClick">{{ squares[0] }}</Square>
      <Square index=1 v-on:squareClick="handleClick">{{ squares[1] }}</Square>
      <Square index=2 v-on:squareClick="handleClick">{{ squares[2] }}</Square>
    </div>
    <div class="row">
      <Square index=3 v-on:squareClick="handleClick">{{ squares[3] }}</Square>
      <Square index=4 v-on:squareClick="handleClick">{{ squares[4] }}</Square>
      <Square index=5 v-on:squareClick="handleClick">{{ squares[5] }}</Square>
    </div>
    <div class="row">
      <Square index=6 v-on:squareClick="handleClick">{{ squares[6] }}</Square>
      <Square index=7 v-on:squareClick="handleClick">{{ squares[7] }}</Square>
      <Square index=8 v-on:squareClick="handleClick">{{ squares[8] }}</Square>
    </div>
  </div>

  <p>Squares: {{ squares }}</p>
  <p>State: {{ state }}</p>
  <p>{{ _statusString }}</p>
  <p>Line: {{ _line }}</p>
</template>

<style scoped>
.row {
  display: flex;
  justify-content: center
}
</style>
