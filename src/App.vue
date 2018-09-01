<template>
  <div id="app">
    <div class="info">
    </div>
    <div class="tabuleiro">
      <div v-for="(row, key, index) in board" class="row" :key="index">
        <div v-for="(item, key, index) in row" :key="index" class="item">{{item}}</div>
      </div>
    </div> 
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    HelloWorld
  },

  data() {
    return {
      size: 8,
      delay: 1000,
      board: [],
    }
  },

  mounted() {
    this.board = this.generateBoard(this.size);
    this.solveNQ();
  },

  methods: {


    printSolution(){
      this.$forceUpdate()
    },

    isSafe(board, row, col){
      // Checks the ← direction
      for(let i=0; i<col; i++){
        if (board[row][i] === 1) {
          return false;
        }
      }
      // Checks the ↖ direction 
      for(let i=row, j=col; i>=0 && j>=0; i--, j--){
        if (board[i][j] === 1) {
          return false;
        }
      }
      // Checks the ↙ direction 
      for(let i=row, j=col; j>=0 && i< this.size; i++, j--){
        if (board[i][j] === 1){
          return false;
        }
      }
      return true;
    },

    recurseNQ(board, col){
      if(col>=this.size){
        return true;
      }
      for(let i=0; i<this.size; i++){
        if(this.isSafe(board, i, col)){
          board[i][col]=1;
          if(this.recurseNQ(board, col+1)===true)
            return true;
          board[i][col]=0;
        }
      }
    },


    solveNQ(){
      this.board = this.generateBoard(this.size);
      if(this.recurseNQ(this.board, 0)===false){
        // console.log("No solution found");
        return false;
      }
      this.printSolution( );
    },

    generateBoard(n){
      var board=[];
      for(var i=0; i<n; i++){
        board[i]=[];
        for(var j=0; j<n; j++){
          board[i][j]=0;
        }
      }
      return board;
    }
  }

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.tabuleiro {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.row {
  flex: 1;
  display: flex;
}

.item {
  width: 25px
}

</style>
