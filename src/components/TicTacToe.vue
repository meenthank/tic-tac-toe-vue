<template>
  <div class="center-screen">
    <div class="mb-5 text-center">
      <h2>Current player : {{ resolveDisplay(player) }}</h2>
    </div>
    <div v-for="(item_y, index_y) in board" :key="index_y" class="game-table">
      <b-row>
        <b-col
          v-for="(item_x, index_x) in item_y"
          :key="index_x"
          :class="`border text-center game-table-grid ${!item_x && !boardState ? 'cursor-pointer' : ''} ${styleCaseWinner(index_y, index_x)}` "
          @click="clickSelect(index_y, index_x)"
        >
          <div style="margin: auto;">
            <span class="text-detail">{{ resolveDisplay(item_x) }}</span>
          </div>
        </b-col>
      </b-row>
    </div>
    <div class="mt-5 text-center">
      <h5>X win {{ xWinner }}, O win {{ oWinner }}, draw {{ bothDraw }}</h5>
    </div>
    <div class="mt-3 text-center">
      <b-button
        variant="outline-secondary"
        size="lg"
        @click="resetBoard"
      >
        Restart
      </b-button>
    </div>
  </div>
</template>

<script>
// import Vue from 'vue'
import {
  BRow,
  BCol,
  BButton,
} from 'bootstrap-vue'

export default {
  components: {
    BRow,
    BCol,
    BButton,
  },

  data() {
    return {
      board: [[0, 0, 0], [0, 0, 0], [0, 0, 0]],
      player: 1,
      playerWinner: null,
      xWinner: 0,
      oWinner: 0,
      bothDraw: 0,
      caseWinner: null,
      winnerRecord: [],
    }
  },

  computed: {
    turnRemain() {
      return this.board.flat().filter(x => x === 0).length
    },

    boardState() {
      if(this.playerWinner) return this.playerWinner === 1 ? 'X Win' : 'O Win'
      if(!this.turnRemain) return 'Draw'
      return null
    }
  },

  methods: {
    clickSelect(y, x) {
      if(!this.board[y][x] && !this.playerWinner)
      {
        this.$set(this.board[y], x, this.player)

        const getResult =  this.checkResult()
        if (getResult)
        {
          this.playerWinner = this.player
          this.$swal(this.boardState);
          if (this.player == 1) this.xWinner += 1
          else this.oWinner += 1
        }
        else
        {
          if(!this.turnRemain)
          {
            this.bothDraw += 1
            this.$swal(this.boardState);
          }
          else this.player = this.player === 1 ? 2 : 1
        }
      }
    },

    resolveDisplay(val) {
      if (val === 1) return 'X'
      if (val === 2) return 'O'
      return ''
    },

    checkResult(){
      let check = (list) => list.every(item => item !== 0 && list.indexOf(item) === 0)

      const case1 = [this.board[0][0], this.board[0][1], this.board[0][2]]
      const case2 = [this.board[0][0], this.board[1][0], this.board[2][0]]
      const case3 = [this.board[0][0], this.board[1][1], this.board[2][2]]
      const case4 = [this.board[0][1], this.board[1][1], this.board[2][1]]
      const case5 = [this.board[0][2], this.board[1][2], this.board[2][2]]
      const case6 = [this.board[1][0], this.board[1][1], this.board[1][2]]
      const case7 = [this.board[2][0], this.board[2][1], this.board[2][2]]
      const case8 = [this.board[0][2], this.board[1][1], this.board[2][0]]

      if (check(case1)) this.caseWinner = 1
      if (check(case2)) this.caseWinner = 2
      if (check(case3)) this.caseWinner = 3
      if (check(case4)) this.caseWinner = 4
      if (check(case5)) this.caseWinner = 5
      if (check(case6)) this.caseWinner = 6
      if (check(case7)) this.caseWinner = 7
      if (check(case8)) this.caseWinner = 8

      if (this.caseWinner) return true
      else return false
    },

    resetBoard() {
      this.player = 1
      this.caseWinner = null
      this.playerWinner = null
      this.board = [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
    },

    styleCaseWinner(y, x) {
      if(y === 0) {
        if(x === 0) return this.caseWinner === 1 || this.caseWinner === 2 || this.caseWinner === 3 ? 'highlight-winner' : ''
        if(x === 1) return this.caseWinner === 1 || this.caseWinner === 4 ? 'highlight-winner' : ''
        if(x === 2) return this.caseWinner === 1 || this.caseWinner === 5 || this.caseWinner === 8 ? 'highlight-winner' : ''
      }

      if(y === 1) {
        if(x === 0) return this.caseWinner === 2 || this.caseWinner === 6 ? 'highlight-winner' : ''
        if(x === 1) return this.caseWinner === 3 || this.caseWinner === 4 || this.caseWinner === 6 || this.caseWinner === 8  ? 'highlight-winner' : ''
        if(x === 2) return this.caseWinner === 5 || this.caseWinner === 6 ? 'highlight-winner' : ''
      }

      if(y === 2) {
        if(x === 0) return this.caseWinner === 2 || this.caseWinner === 7 || this.caseWinner === 8 ? 'highlight-winner' : ''
        if(x === 1) return this.caseWinner === 4 || this.caseWinner === 7 ? 'highlight-winner' : ''
        if(x === 2) return this.caseWinner === 3 || this.caseWinner === 5 || this.caseWinner === 7 ? 'highlight-winner' : ''
      }
    }
  },
}
</script>

<style>
.cursor-pointer {
  cursor: pointer;
}

.center-screen {
    position: absolute;
    top: 50%;
    left: 50%;
    margin-right: -50%;
    transform: translate(-50%, -50%);
}

.game-table {
  width: 600px;
}

.game-table-grid {
  display: flex;
  height: 100px;
}

.text-detail {
  font-size: 36px;
}

.highlight-winner {
  background-color: greenyellow;
}
</style>