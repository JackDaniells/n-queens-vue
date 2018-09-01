<template>
  <div id="app">
    <div class="info">
      <span class="text" style="margin-bottom: 3px" v-if="execError">Nenhuma solução encontrada</span>
      <span class="text" style="margin-bottom: 3px" v-if="execCompleted">Comparações: {{comp}}</span>
      <div class="type-execution" style="margin-bottom: 3px">
        <button style="width: 100px" :class="execType === 1 ? 'isSelected' : 'notSelected'" v-on:click="execType = 1">Backtracking</button>
        <button style="width: 100px" :class="execType === 2 ? 'isSelected' : 'notSelected'" v-on:click="execType = 2">outro</button>
      </div>
      <input style="width: 190px; margin-bottom: 3px" v-model="size" type="number"/>
      <button style="width: 200px; margin-bottom: 3px" v-on:click="startExec()">Iniciar</button>
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
      execError: false,
      execCompleted: false,
      size: 8,
      delay: 1000,
      execTime: 0,
      board: [],
      comp: 0,
      execType: 1
    }
  },

  mounted() {
   
  },

  methods: {

    startExec() {
      this.comp = 0;
      this.execCompleted = false;
      if (this.execType === 1) {
        this.solveWithBacktracking();
      } else {

      }
    
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
    },

    printSolution(){
      this.$forceUpdate()
    },

   

    /***************************************
     *        backtracking solution
     * ************************************/
    solveWithBacktracking(){
      this.board = this.generateBoard(this.size);
      if(this.bktkEngine(this.board, 0) === false){
        this.execError = true
        return false;
      }
      this.execCompleted = true;
      this.printSolution();
    },

    isSafeBacktrack(board, row, col){
      // Checks the ← direction
      for(let i=0; i<col; i++){
        if (board[row][i] === 1) {
          this.comp++;
          return false;
        }
      }
      // Checks the ↖ direction 
      for(let i=row, j=col; i>=0 && j>=0; i--, j--){
        if (board[i][j] === 1) {
          this.comp++;
          return false;
        }
      }
      // Checks the ↙ direction 
      for(let i=row, j=col; j>=0 && i< this.size; i++, j--){
        if (board[i][j] === 1){
          this.comp++;
          return false;
        }
      }
      return true;
    },

    bktkEngine(board, col){
      if(col>=this.size){
        return true;
      }
      for(let i=0; i<this.size; i++){
        if(this.isSafeBacktrack(board, i, col)){
          board[i][col]=1;
          if(this.bktkEngine(board, col+1)===true)
            return true;
          board[i][col]=0;
        }
      }
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

.isSelected {
  background-color: #999;
  border: 2px solid #999
}

.notSelected {
  background-color: #DDD;
}

.item {
  width: 25px
}

.info {
  padding-bottom: 10px;
  display: flex;
  flex-direction: column;
  align-items:center;
  justify-content: center;
}

</style>
