<template>
  <div id="app">
    <div class="info">
      <span class="text" style="margin-bottom: 3px" v-if="execError">Nenhuma solução encontrada</span>
      <span class="text" style="margin-bottom: 3px" v-if="execCompleted">Comparações: {{comp}}</span>
      <div class="type-execution" style="margin-bottom: 3px">
        <button style="width: 100px" :class="execType === 1 ? 'isSelected' : 'notSelected'" v-on:click="execType = 1">Backtracking</button>
        <button style="width: 100px" :class="execType === 2 ? 'isSelected' : 'notSelected'" v-on:click="execType = 2">Hill Climbing</button>
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
        this.board = this.solveNQueens(this.generateState(this.size))
        console.log(this.board)
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
    },

    /*********************************************** */

    // Count the number or row collisions
    rowCollisions: function (a) {
      var collision = 0;
      for (var i in a) {
        for (var j in a) {
          if (j != i) {
            collision = a[i] == a[j] ? collision+1 : collision;
          }
        }
      }
      return collision;
    },

    // Count the number of column collisions
    diaCollisions: function (a) {
      var collision = 0;
      for (var i in a){
        for (var j in a){
          if (i != j) {
            var dp = Math.abs(i-j);
            collision = a[i] == a[j]+dp ? collision+1 : collision;
            collision = a[i] == a[j]-dp ? collision+1 : collision;
          }
        }
      }
      return collision / 2;
    },

    // Heuristic Evaluation Function for N Queens
    // We will want to minimize collisions -- also referred to as min-conflicts in general
    evaluate: function (s) {
      return this.diaCollisions(s) + this.rowCollisions(s);
    },

    // Generate a set of candidates.
    generateCandidates: function(current) {
        var candidates = [];
        for (var i = 0; i < current.length; i++) {
            var start = current.slice(0, i);
            var end = current.slice(i+1,current.length);
            for (var j = 1; j <= current.length; j++) {
                var c = start.concat([(Math.floor((Math.random()*current.length)+1))].concat(end));
                candidates.push(c);
            }
        }
        return candidates;
    },

    // Generate a random new state for the N Queens problem of size n
    generateState: function(n) {
        var state = [];
        for(var i = 0; i < n; i++){
            state[i] = Math.floor(Math.random() * n + 1);
        }
        return state;
    },

    // Helper Function to tell us if our configuration is a solution.
    isSolution: function (config) {
        return (this.evaluate(config) === 0);
    },

    // Workhorse function that solves our puzzle.
    nQueensBestFirstHillClimbing: function (start) {
        var best = start;
        var current;
        var currentEval = this.evaluate(start);

        while (true) {
            current = best;
            var candidates = this.generateCandidates(current);
            for (var i in candidates){
              var candidateEval = this.evaluate(candidates[i]);
              // Lower evaluation number is better for our implementation
              if (candidateEval < currentEval) {
                  current =  candidates[i] ;
                  currentEval = candidateEval;
              }
            }

            // If current & best are STILL the same, then we reached a peak.
            if (best == current)
                return best;

            best = current;
        }
    },

    // Solve the N Queens problem using Random Restart Hill Climbing.
    solveNQueens: function(state) {
        var count = 1;
        state = this.nQueensBestFirstHillClimbing(state);

        while (!this.isSolution(state)){
            // Random Restart If not a Solution.
            state = this.generateState(state.length);
            count++;
            state = this.nQueensBestFirstHillClimbing(state);
        }

        // Return the Number of Hill Climbing Random Restarts & the SOlution.
        return [count, state];
    },

    //const solver = solveNQueens([1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,10];
    // console.log(solveNQueens(generateState(12)))
    //console.log(solver)


    /*********************************************** */

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
