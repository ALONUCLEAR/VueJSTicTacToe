<template>
  <div class="board container">
    <div v-for="(row, index) in cells" :key="index" class="row justify-content-center">
      <i v-for="(cell, colIndex) in row" :key="colIndex" class="fa-solid square">
        {{cell}}
      </i>
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
    }
  },
  created() {
    this.cells = Array.from({length: this.rows}).map(() => Array.from({length: this.columns}, () => 'x'));
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