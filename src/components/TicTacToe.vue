<template>
  <div class="game-board">
    <table cellspacing=0>
      <tr v-for="(row, rowIndex) in game" v-bind:key="rowIndex">
        <td v-for="(column, columnIndex) in row" v-bind:key="columnIndex"
          v-on:click="click(rowIndex, columnIndex)">
          <transition name="fade">
            <span v-if="game[rowIndex][columnIndex]">{{ game[rowIndex][columnIndex] }}</span>
          </transition>
        </td>
      </tr>
    </table>
    <button class="reset-button" v-if="emptyCellCount() !== 9" v-on:click="reset()">RESTART</button>
    <p v-if="winner">Game is over! Winner is {{winner}}.</p>
    <p v-if="draw">Game is over! Game is draw.</p>
  </div>
</template>

<script>
export default {
  name: 'game',
  methods: {
    isDone: function isDone() {
      let result = false;
      if (this.game[0][0] !== '' && this.game[0][0] === this.game[1][1] && this.game[1][1] === this.game[2][2]) {
        result = true;
        this.winner = this.game[0][0].toString();
      } else if (this.game[0][2] !== '' && this.game[0][2] === this.game[1][1] && this.game[1][1] === this.game[2][0]) {
        result = true;
        this.winner = this.game[0][2].toString();
      } else {
        for (let i = 0; i < 3; i += 1) {
          if (this.game[i][0] !== '' && this.game[i][0] === this.game[i][1] && this.game[i][1] === this.game[i][2]) {
            result = true;
            this.winner = this.game[i][0].toString();
            break;
          } else if (this.game[0][i] !== '' && this.game[0][i] === this.game[1][i] && this.game[1][i] === this.game[2][i]) {
            result = true;
            this.winner = this.game[0][i];
            break;
          }
        }
      }
      if (!this.winner) {
        if (!this.emptyCellCount()) {
          result = true;
          this.draw = true;
        }
      }
      return result;
    },
    emptyCellCount: function emptyCellCount() {
      let emptyCount = 0;
      for (let i = 0; i < 3; i += 1) {
        for (let j = 0; j < 3; j += 1) {
          if (this.game[i][j] === '') {
            emptyCount += 1;
          }
        }
      }
      return emptyCount;
    },
    moveComputer: function moveComputer() {
      while (true) {
        const randomX = Math.floor(Math.random() * 3);
        const randomY = Math.floor(Math.random() * 3);
        if (this.game[randomX][randomY] === '') {
          this.game[randomX][randomY] = 'O';
          break;
        }
      }
    },
    click: function click(x, y) {
      if (this.game[x][y] || this.winner || this.turn === 'O') {
        return;
      }
      this.turn = 'O';
      this.game[x][y] = 'X';
      if (!this.isDone()) {
        setTimeout(() => {
          this.moveComputer();
          this.isDone();
          this.$forceUpdate();
          this.turn = 'X';
        }, 1000);
      }
      this.$forceUpdate();
    },
    reset: function reset() {
      this.game = [['', '', ''], ['', '', ''], ['', '', '']];
      this.winner = null;
      this.draw = false;
      this.turn = 'X';
    },
  },
  data() {
    return {
      turn: 'X',
      winner: null,
      draw: false,
      game: [
        ['', '', ''],
        ['', '', ''],
        ['', '', ''],
      ],
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.game-board table{
  margin: 0 auto;
}

.game-board td {
  width: 60px;
  height: 60px;
  line-height: 60px;
  text-align: center;
  font-size: 30px;
}

.game-board tr td:first-child,
.game-board tr td:nth-child(2) {
  border-right: 1px solid #ccc;
}

.game-board tr:first-child td,
.game-board tr:nth-child(2) td {
  border-bottom: 1px solid #ccc;
}

.game-board {
  margin-top: 100px;
}

.reset-button {
  padding: 5px 10px;
  border-radius: 10px;
  border: none;
  background-color: gray;
  margin-top: 20px;
  color: antiquewhite;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}

.fade-enter, .fade-leave {
  opacity: 0;
}
</style>
