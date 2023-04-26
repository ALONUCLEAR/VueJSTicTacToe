<template>
  <div class="board container">
    <div class="pb-3 row d-flex align-items-center justify-content-center"> 
      <button class="btn btn-primary mr-5" @click="initiateBoard">RESET</button>
      <span class="px-5" />
      <toggle :text="toggleText" @swap="swapAI" :checked="isAI" />
    </div>
    <div v-for="(row, index) in cells" :key="index" class="row justify-content-center">
      <i v-for="(cell, colIndex) in row" :key="colIndex" class="square" :class="cell" @click="clickCell(index, colIndex)" />
    </div>    
  </div>
</template>

<script>
import swal from 'sweetalert2';
import Toggle from './Toggle.vue';
const copyBoard = board => [...board.map(row => [...row])];

export default {
  name: 'GameBoard',
  props: {
    rows: {
      type: Number,
      default: 3,
    },
    columns: {
      type: Number,
      default: 3,
    }
  },
  components: {
    Toggle
  },
  data() {
    return {
      cells: [],
      circle: "fa-regular fa-circle",
      x: "fa-solid fa-x",
      isXTurn: true,
      isAI: false,
    }
  },
  created() {
    this.initiateBoard();
    this.isAI = Boolean(localStorage.getItem('isAI')) || false;
  },
  methods: {
    swapAI() {
      this.isAI = !this.isAI;
      localStorage.setItem('isAI', this.isAI);

      if(this.isAI && !this.isXTurn) {
        this.aiPlay();
      }
    },
    initiateBoard() {
      this.cells = Array.from({length: this.rows}).map(() => Array.from({length: this.columns}, () => '')); 
      this.isXTurn = true;
    },
    error(text, title="Error!") {
      swal.fire({icon: "error", title, text})
    },
    win(title, text) {
      swal.fire({icon: "success", title, text})
    },
    draw() {
      swal.fire({icon: "info", title: "Draw!", text: "No winners, no losers."})
    },
    clickCell(row, col) {
      if(this.didGameEnd()) {
        this.error("The game has ended, If you want to play another game, press reset.", "Hold on!");
      }
      else if(this.cells[row][col].trim() !== "") {
        this.error("Cell is already clicked, try another one.");  
      } else {
        this.cells[row][col] = this.isXTurn ? this.x : this.circle;
        this.isXTurn = !this.isXTurn;
        this.$forceUpdate();
        this.announceEnd();

        if(!this.didGameEnd() && this.isAI && !this.isXTurn) {
          this.aiPlay();
        }
      }
    },
    announceEnd() {
      if(this.didShapeWin('x')) {
        this.win("X won!", "Good job x. You won the game!");
      } else if(this.didShapeWin('o')) {
        this.win("O won!", "Good job o. You won the game!");
      } else if(this.isDraw()) {
        this.draw();
      }
    },
    isBoardAtDraw(board) {
      return board.every(row => row.every(cell => cell !== ''));
    },
    isDraw() {
      return this.isBoardAtDraw(this.cells);
    },
    aiPlay() {
      const options = this.possibleActions(this.cells, 'o')
      .map(action => ({row: action.row, col: action.col, value: this.minimax(this.result(this.cells, action), true)}));
      
      if(options.length > 0) {
        const {row, col} = options.find(option => option.value === Math.min(...options.map(({value}) => value)));
        this.clickCell(row, col);
      }
    },
    didShapeBeatBoard(symbol, board) {
      const shape = symbol === 'x' ? this.x : this.circle;
      
      for (let rowIndex = 0; rowIndex < this.rows; rowIndex++) {
        let count = 0;
        for (let columnIndex = 0; columnIndex < this.columns; columnIndex++) {
          if (board[rowIndex][columnIndex] === shape) {
            count++;
          }
        }
        if (count === this.columns) {
          return true;
        }
      }

      for (let columnIndex = 0; columnIndex < this.columns; columnIndex++) {
        let count = 0;
        for (let rowIndex = 0; rowIndex < this.rows; rowIndex++) {
          if (board[rowIndex][columnIndex] === shape) {
            count++;
          }
        }
        if (count === this.rows) {
          return true;
        }
      }

      let count = 0;
      for (let rowIndex = 0; rowIndex < this.rows; rowIndex++) {
        if (board[rowIndex][rowIndex] === shape) {  
          count++;
        }
      }
      if (count === this.rows) {
        return true;
      }

      count = 0;
      for (let rowIndex = 0; rowIndex < this.rows; rowIndex++) {
        if (board[rowIndex][this.columns - 1 - rowIndex] === shape) {
          count++;
        }
      }
      if (count === this.rows) {
        return true;
      }

      return false;
    },
    didShapeWin(shape) {
      return this.didShapeBeatBoard(shape, this.cells);
    },
    didGameEnd() {
      return this.didShapeWin('x') || this.didShapeWin('o') || this.isDraw();
    },
    isTerminal(state) {
      return this.didShapeBeatBoard('x', state) || this.didShapeBeatBoard('o', state) || this.isBoardAtDraw(state); 
    },
    valueTerminalState(state) {
      if(this.didShapeBeatBoard('x', state)) {
        return 1;
      } else if(this.didShapeBeatBoard('o', state)) {
        return -1;
      } else if(this.isBoardAtDraw(state)) {
        return 0;
      }

      this.error("Tried to evaluate a non-terminal state");
    },
    result(state, action) { //action is of form {row, col, value}
      const res = copyBoard(state);
      res[action.row][action.col] = action.value === 'x' ? this.x : this.circle;

      return res;
    },
    possibleActions(board, shape) {
      return board.flatMap((row, rowIndex) => row.map((_, colIndex) => 
                  ({row: rowIndex, col: colIndex, value: shape})).filter(({col}) => !row[col]));
    },
    minimax(board, isXTurn) {
      if(this.isTerminal(board)) {
        return this.valueTerminalState(board);
      }

      if(isXTurn) {
        return Math.max(...this.possibleActions(board, 'x').map(action => this.minimax(this.result(board, action), false)));
      }
      
      return Math.min(...this.possibleActions(board, 'o').map(action => this.minimax(this.result(board, action), true)));
    }
  },
  computed: {
    toggleText() {
      return this.isAI ? "AI on" : "AI off";
    }
  }
}
</script>

<style scoped>
.dark .square {
  border: 1px solid white;
}

.square {
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid black;
  width: 100px;
  height: 100px;
}
</style>