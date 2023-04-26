<template>
  <div class="main">
    <Board />
  </div>
</template>

<script>
// @ is an alias to /src
import Board from '@/components/Board.vue'

export default {
  name: 'MainPage',
  components: {
    Board
  }
}
</script>
