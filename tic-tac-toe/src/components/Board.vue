<template>
  <div class="board container">
    <div v-for="(row, index) in cells" :key="index" class="row justify-content-center">
      <i v-for="(cell, colIndex) in row" :key="colIndex" class="square" :class="cell"/>
    </div>    
  </div>
</template>

<script>
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
    }
  },
  created() {
    this.cells = Array.from({length: this.rows}).map(() => Array.from({length: this.columns}, () => '')); 
    console.log(this.cells);
  }
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