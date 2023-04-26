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
    console.log(this.cells);
  },
  methods: {
    error(text, title="Error!") {
      swal.fire({icon: "error", title, text})
    },
    clickCell(row, col) {
      if(this.cells[row][col].trim() !== "") {
        this.error("Cell is already clicked, try another one.");  
      } else {
        this.cells[row][col] = this.isXTurn ? this.x : this.circle;
        console.log(this.cells[row][col]);
        this.isXTurn = !this.isXTurn;
        this.$forceUpdate();
      }
    }
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