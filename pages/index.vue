<template>
  <div id="app">
    <h1>Shut the Box</h1>
    <p>Welcome to Shut the Box game. <br> If you're new to this game, <a href="https://www.mastersofgames.com/rules/shut-box-rules.htm" target="_blank">click here to learn how to play.</a> Otherwise, enjoy!</p>
    <section class="start" v-if="IsStart">
      <button ref="butt" @click="rollDice()" v-if="IsStart">Start</button>
    </section>
    <section class="game" v-else>
      <section class="gameover-lost" v-if="IsLoser">
        You Lose
        <button @click="reset()">Play Again</button>
      </section>
      <section class="gameover-win" v-if="IsWinner">You Win</section>
      <div class="gameboard">
        <Box :tabs="boxTabs" v-if="boxTabs" />
        <div class="dice-group">
          <div class="dice">
            <Die :rollNumber="die1" />
            <Die :rollNumber="die2" />
          </div>
          <button @click="rollDice()">Roll Dice</button>
        </div>
        <div v-if="moves && moves.length">
          <div class="row">
            <span class="label">Move</span>
            <span></span>
          </div>
          <div class="row" v-for="(move, index) of moves" :key="index" @mouseover="hoverMoves(move)">
            <span>{{move}}</span>
            <button @click="makeMove(move)">Select</button>
          </div>
        </div>
      </div>
    </section>
    <footer>
      <div><a href="https://www.mastersofgames.com/rules/shut-box-rules.htm" target="_blank">How to Play</a></div>
      <div>Made by <a href="https://spaghet.me" target="_blank">tones</a></div>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      IsLoser: false,
      IsWinner: false,
      IsStart: true,
      die1: null,
      die2: null,
      boxTabs: null,
      moves: null,
      rules: {
    '2': [[2]],
    '3': [
        [3],
        [1,2],
    ],
    '4': [
        [4],
        [1,3]
    ],
    '5': [
        [5],
        [1,4],
        [2,3]
    ],
    '6': [
        [6],
        [1,5],
        [2,4],
        [1,2,3]
    ],
    '7': [
        [7],
        [1,6],
        [2,5],
        [3,4],
        [1,2,4]
    ],
    '8': [
        [8],
        [1,7],
        [2,6],
        [3,5],
        [1,2,5],
        [1,3,4]
    ],
    '9': [
        [9],
        [1,8],
        [2,7],
        [3,6],
        [4,5],
        [1,2,6],
        [1,3,5],
        [2,3,4]
    ],
    '10': [
        [1,9],
        [2,8],
        [3,7],
        [4,6],
        [1,2,7],
        [1,3,6],
        [1,4,5],
        [2,3,5],
        [1,2,3,4]
    ],
    '11': [
        [2,9],
        [3,8],
        [4,7],
        [5,6],
        [1,2,8],
        [1,3,7],
        [1,4,6],
        [2,3,6],
        [2,4,5],
        [1,2,3,5]
    ],
    '12': [
        [3,9],
        [4,8],
        [5,7],
        [1,2,9],
        [1,3,8],
        [1,4,7],
        [1,5,6],
        [2,3,6],
        [2,4,5],
        [3,4,5],
        [1,2,3,6]
    ],
}
    }
  },
  async mounted() {
    await this.reset();
  },
  methods: {
    async reset() {
      this.IsLoser = false;
      this.IsWinner = false;
      this.IsStart = true;
      this.die1 = null;
      this.die2 = null;
      this.boxTabs = [
        { id: 1, isFlipped: false, isPossible: false },
        { id: 2, isFlipped: false, isPossible: false },
        { id: 3, isFlipped: false, isPossible: false },
        { id: 4, isFlipped: false, isPossible: false },
        { id: 5, isFlipped: false, isPossible: false },
        { id: 6, isFlipped: false, isPossible: false },
        { id: 7, isFlipped: false, isPossible: false },
        { id: 8, isFlipped: false, isPossible: false },
        { id: 9, isFlipped: false, isPossible: false }
      ];
      this.moves = [];
    },
    getButtonText() {
      return this.IsStart ? 'Start' : 'Roll';
    },
    roll() {
      return Math.round(Math.random() * 5) + 1;
    },
    rollDice() {
      this.IsStart = false;
      this.die1 = this.roll();
      this.die2 = this.roll();
      this.moves = [];
      this.moves = this.calculatePossibleMoves();

      if (!this.moves.length) {
        this.IsLoser = true;
      }
    },
    calculatePossibleMoves() {
        const dieTotal = this.die1 + this.die2;
        const dieMoves = this.rules[dieTotal];
        let validMoves = [];

        for (let move of dieMoves) {
          let isValid = true;

          for (let val of move) {
            if (this.boxTabs[val - 1].isFlipped) {
              isValid = false;
            }
          }

          if (isValid) {
            validMoves.push(move);
          }
        }

        return validMoves;
    },
    hoverMoves(move) {
      this.resetPending();

      for (let num of move) {
        this.boxTabs[num - 1].isPossible = true;
      }
    },
    resetPending() {
      for (let tab of this.boxTabs) {
        tab.isPossible = false;
      }
    },
    makeMove(move) {
      this.resetPending();
      
      for (let num of move) {
        this.boxTabs[num - 1].isFlipped = true;
      }

      this.moves = [move];
    }
  }
}
</script>

<style>
html, body { padding: 0; margin: 0; }

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  min-height: 100vh;
  height: 100%;
  display: flex;
  flex-direction: column;
  padding: 0.5rem;
  background: #F0F8EA;
}

button {
  border: 1px solid white;
  background: lightskyblue;
  padding: 0.5rem 1rem;
  cursor: pointer;
}

button:hover {
  background: rgb(197, 233, 255);
}

footer {
  background: #F0F66E;
  margin-top: auto;
}

.game {
  padding: 0 1rem;
}

.gameboard {
  margin-top: 1rem;
}

.dice {
  display: flex;
  justify-content: space-between;
  max-width: 100px;
  margin: 0 auto;
}

.row {
  display: flex;
  justify-content: space-between;
  margin: 0.5rem 0;
}

.label {
  font-weight: bold;
  text-decoration: underline;
}

@media screen and (min-width: 765px) {
  .game {
    max-width: 500px;
    margin: 0 auto;
  }
}
</style>
