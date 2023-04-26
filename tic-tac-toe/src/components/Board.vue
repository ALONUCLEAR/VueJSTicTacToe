<template>
  <div class="board container">
    <div v-for="(row, index) in cells" :key="index" class="row justify-content-center">
      <i v-for="(cell, colIndex) in row" :key="colIndex" class="square" :class="cell" @click="clickCell(index, colIndex)" />
    </div>    
  </div>
</template>

<script>
import swal from 'sweetalert2';

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
  data() {
    return {
      cells: [],
      circle: "fa-regular fa-circle",
      x: "fa-solid fa-x",
      isXTurn: true,
    }
  },
  created() {
    this.cells = Array.from({length: this.rows}).map(() => Array.from({length: this.columns}, () => '')); 
  },
  methods: {
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
    isDraw() {
      return this.cells.every(row => row.every(cell => cell !== ''));
    },
    didShapeWin(shape) {
      const board = this.cells.map(row => row.map(cell => {
        if(cell === this.x) {
          return 'x'
        }

        return cell === this.circle ? 'o' : '';
      }));

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
    didGameEnd() {
      return this.didShapeWin('x') || this.didShapeWin('o') || this.isDraw();
    },
  },
}
</script>

<style scoped>
.square {
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid black;
  width: 100px;
  height: 100px;
  outline-color: black;
  outline-width: thick;
}
</style>