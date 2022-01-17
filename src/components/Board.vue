<script setup>
  import { ref, computed } from 'vue'
  import Square from './Square.vue'

  // state id
  const STATE_PLAY = 0    // non-terminal state
  const STATE_WIN = 1     // terminal state -- win
  const STATE_DRAW = 2    // terminal state -- draw

  // constants
  const BOARD_SIZE = 9

  const STATUS = [
    "Next Player: ",  // STATE_PLAY (0)
    "Winner: ",       // STATE_WIN (1)
    "Draw"            // STATE_DRAW (2)
  ]

  const PLAYER = [
    "X",              // PLAYER_0 (0)
    "O"               // PLAYER_1 (1)
  ]

  // state
  const _state = ref(STATE_PLAY)   // STATE_PLAY, STATE_WIN, STATE_DRAW
  const _squares = ref(Array(BOARD_SIZE).fill(null))   // [ "0","X",null, "O","X",null, null,"X",null]
  const _winner = ref(null)       // "X" | "O" | null (no winner)
  const _line = ref(null)         // [1, 4, 7] | null (no winner)

  const _statusString = computed(() => {
    if (_winner.value) {
      return STATUS[_state.value] + _winner.value
    }
    else if (isDraw(_squares.value)) {
      return STATUS[_state.value]
    }
    else {
      return STATUS[_state.value] + nextPlayer(_squares.value)
    }
  })

  // TODO
  function restart() {
    _squares.fill(null)

  }

  //
  function countEmpty(squares) {
    let count = 0
    squares.forEach(c => count += !c)
    return count
  }

  //
  function countFilled(squares) {
    let count = 0
    squares.forEach(c => count += !!c)
    return count
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

    // test for winner
    for (let i = 0; i < lines.length; i++) {
      const [a, b, c] = lines[i];
      if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
        return { winner: squares[a], line: lines[i] }
      }
    }
    return { winner: null , line: null }
  }

  //
  function handleClick(squareIndex) {
    console.log('handleClick: ', squareIndex)
    if ( _squares.value[squareIndex]) {
      console.log('Ignore Click (square filled): ', squareIndex)
      return
    }

    if (_state.value !== STATE_PLAY) {
      console.log('Ignore Click (game stopped): ', squareIndex)
      return
    }

    // new move
    console.log('New Move: ', squareIndex)
    const player = nextPlayer(_squares.value)
    _squares.value[squareIndex] = player

    const { winner, line } = calculateWinner(_squares.value)
    _winner.value = winner
    _line.value = line

    if (_winner.value) {
      _state.value = STATE_WIN
    }
    else if (isDraw(_squares.value)) {
      _state.value = STATE_DRAW
    }
    else {
      _state.value = STATE_PLAY
    }
 }

</script>

<template>
  <h2>Board</h2>
  <div>
    <div class="row">
      <Square index=0 v-on:squareClick="handleClick">{{ _squares[0] }}</Square>
      <Square index=1 v-on:squareClick="handleClick">{{ _squares[1] }}</Square>
      <Square index=2 v-on:squareClick="handleClick">{{ _squares[2] }}</Square>
    </div>
    <div class="row">
      <Square index=3 v-on:squareClick="handleClick">{{ _squares[3] }}</Square>
      <Square index=4 v-on:squareClick="handleClick">{{ _squares[4] }}</Square>
      <Square index=5 v-on:squareClick="handleClick">{{ _squares[5] }}</Square>
    </div>
    <div class="row">
      <Square index=6 v-on:squareClick="handleClick">{{ _squares[6] }}</Square>
      <Square index=7 v-on:squareClick="handleClick">{{ _squares[7] }}</Square>
      <Square index=8 v-on:squareClick="handleClick">{{ _squares[8] }}</Square>
    </div>
  </div>

  <p>Squares: {{ _squares }}</p>
  <p>State: {{ _state }}</p>
  <p>{{ _statusString }}</p>
  <p>Line: {{ _line }}</p>
</template>

<style scoped>
.row {
  display: flex;
  justify-content: center
}
</style>
