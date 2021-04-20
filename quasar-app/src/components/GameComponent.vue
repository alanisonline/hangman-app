<template>
  <div class="game full-width">
    <div class="output">
      <div class="row justify-evenly full-width">
        <p>LIVES: {{ this.lives }}</p>
        <p>TIME: {{ this.time }}</p>
      </div>
      <canvas id="gameCanvas"></canvas>
    </div>
    <div v-show="this.state == 0" class="game-menu">
      <div class="q-pa-lg">
        <q-option-group v-model="difficultyLevel" :options="difficultyLevels" />
      </div>
      <q-btn
        id="start"
        class="full-width"
        color="light-green-7"
        v-on:click="startGame"
      >
        Start
      </q-btn>
    </div>
    <div
      v-show="this.state == 1 || this.state == 2"
      class="word row justify-evenly full-width"
    >
      <div
        class="letter-box"
        v-for="letter in targetWord.letters"
        :key="letter.character.id"
      >
        <span v-show="letter.active == 1">{{ letter.character }}</span>
      </div>
    </div>
    <div v-show="this.state == 1" class="input">
      <q-btn
        round
        v-for="letter in alphabet.letters"
        v-show="letter.active == 1"
        v-on:click="select(letter)"
        :key="letter.character.id"
      >
        {{ letter.character }}
      </q-btn>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

class Word {
  word: string;
  letters: Array<Letter>;

  constructor(word = <string>'hangman', deactivate = false) {
    this.word = word.toUpperCase();
    this.letters = this.lettersFromWord(deactivate);
  }
  lettersFromWord(deactivate = false): Letter[] {
    let letters: Letter[] = [];
    this.word.split('').forEach(function (character) {
      let letter = new Letter(character);
      if (deactivate) {
        letter.active = 0;
      }
      letters.push(letter);
    });
    return letters;
  }
}

class Letter {
  public character: string;
  public active: number;
  constructor(character = 'h', active = 1) {
    this.character = character.toUpperCase();
    this.active = active;
  }
}

export default Vue.extend({
  name: 'Game',
  props: {
    msg: String,
  },
  data(): {
    state: number;
    alphabet: Word;
    difficultyLevel: number;
    difficultyLevels: { label: string; value: number; color: string }[];
    lives: number;
    targetWord: Word;
    time: number;
    timer: number;
    completionTime: number;
  } {
    const state = 0;
    const alphabet = new Word('ABCDEFGHIJKLMNOPQRSTUVWXYZ');
    const difficultyLevel = 3;
    const difficultyLevels = [
      {
        label: 'Easy',
        value: 1,
        color: 'green',
      },
      {
        label: 'Medium',
        value: 2,
        color: 'orange',
      },
      {
        label: 'Hard',
        value: 3,
        color: 'red',
      },
    ];
    const lives = 6;
    const targetWord: Word = new Word(undefined, true);
    const time = 0;
    const timer = 0;
    const completionTime = 0;
    return {
      state,
      alphabet,
      difficultyLevel,
      difficultyLevels,
      lives,
      targetWord,
      time,
      timer,
      completionTime,
    };
  },
  methods: {
    startGame() {
      this.resetGame();
      this.startTimer();
    },
    resetGame() {
      this.setTargetWord();
      this.resetAlphabet();
      this.lives = 6;
      this.state = 1;
    },
    resetAlphabet() {
      this.alphabet.letters.forEach((letter) => {
        letter.active = 1;
      });
    },
    startTimer() {
      this.timer = window.setInterval(() => {
        this.updateTimer();
      }, 1000);
    },
    updateTimer() {
      this.time++;
    },
    stopTimer() {
      this.completionTime = this.time;
      window.clearInterval(this.timer);
    },
    select(button: Letter): void {
      button.active = button.active == 1 ? 0 : 1;
      let isWordComplete = true;
      let loseLife = true;
      this.targetWord.letters.forEach((letter: Letter) => {
        if (letter.character == button.character) {
          letter.active = 1;
          loseLife = false;
        }
        if (letter.active == 0) isWordComplete = false;
      });
      if (loseLife && this.lives > 0) this.lives--;
      if (isWordComplete) this.wordComplete();
    },
    wordComplete(): void {
      this.stopTimer();
      this.state = 0;
    },
    setTargetWord(): Word {
      let word: Word;
      switch (this.difficultyLevel) {
        case 1:
          word = new Word('dog', true);
          break;
        case 2:
          word = new Word('garden', true);
          break;
        case 3:
          word = new Word('intrinsic', true);
          break;
        default:
          word = new Word('hangman', true);
          break;
      }
      this.targetWord = word;
      return this.targetWord;
    },
    difficulty(setting: number) {
      if (setting >= 1 && setting <= 3) {
        this.difficultyLevel = setting;
      }
      return this.difficultyLevel;
    },
  },
});
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
.input {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr;
}
.input button {
  margin: 4px;
}
.game {
  min-width: 320px;
  max-width: 100vw;
}
.letter-box {
  border-bottom: 2px solid black;
  min-width: 20px;
  max-width: 50px;
  height: 50px;
}
</style>
