<template>
  <div class="game full-width">
    <div class="output">
      <div class="row full-width">
        <p>LIVES: {{ this.lives }}</p>
      </div>
      <canvas id="gameCanvas"></canvas>
    </div>
    <div v-show="this.state == 0" class="game-menu">
      <q-btn id="start" v-on:click="startGame"> Start </q-btn>
      <div class="q-pa-lg">
        <q-option-group v-model="group" :options="options" color="primary" />
      </div>
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
  data() {
    const state = 0;
    const alphabet = new Word('ABCDEFGHIJKLMNOPQRSTUVWXYZ');
    const difficultyLevel = 3;
    const lives = 6;
    const targetWord: Word = new Word(undefined, true);
    return {
      state,
      alphabet,
      difficultyLevel,
      lives,
      targetWord,
      group: 'op1',
      options: [
        {
          label: 'Option 1',
          value: 'op1',
        },
        {
          label: 'Option 2',
          value: 'op2',
        },
        {
          label: 'Option 3',
          value: 'op3',
        },
      ],
    };
  },
  methods: {
    startGame() {
      this.state = 1;
    },
    select(button: Letter): void {
      button.active = button.active == 1 ? 0 : 1;
      let isWordComplete = true;
      let loseLife = true;
      this.targetWord.letters.forEach((letter: Letter) => {
        if (letter.character == button.character) {
          letter.active = 1;
          console.log(letter.character + ' == ' + button.character);
          loseLife = false;
        }
        if (letter.active == 0) isWordComplete = false;
      });
      if (loseLife && this.lives > 0) this.lives--;
      if (isWordComplete) this.wordComplete();
    },
    wordComplete(): void {
      this.state = 0;
      alert('success');
    },
    setTargetWord(): void {
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
    },

    difficulty(setting: number) {
      if (setting >= 1 && setting <= 3) {
        this.difficultyLevel = setting;
      }
      return this.difficultyLevel;
    },
  },
  mounted(): void {
    this.setTargetWord();
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
  padding: 8px 0px;
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
