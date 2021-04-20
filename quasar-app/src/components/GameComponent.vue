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
    render(): void {
      let cvs = <HTMLCanvasElement>document.getElementById('gameCanvas');
      let ctx = <CanvasRenderingContext2D>cvs.getContext('2d');
      ctx.clearRect(0, 0, cvs.width, cvs.height);
      let gridSize = { w: cvs.width / 32, h: cvs.height / 24 };

      switch (this.lives) {
        case 1:
          case 2:
            case 3:
              case 4:
                ctx.fillRect(gridSize.w*22, gridSize.h*8, gridSize.w, gridSize.h*4);
                case 5:
          ctx.beginPath();
          ctx.arc((gridSize.w*22) + (gridSize.w/2),gridSize.h*6,gridSize.h*2,0,  2 * Math.PI);
          ctx.stroke();
        case 6:
          this.drawGallow(ctx, gridSize);
          break;
        case 0:
        default:
          ctx.font = '160px Monospace';
          ctx.fillText('HANGMAN', 0, cvs.height - 40, cvs.width);
          break;
      }
    },
    drawGallow(ctx: CanvasRenderingContext2D, gridSize = {w: 32, h: 24}) {
      ctx.fillRect(gridSize.w * 30, 0, gridSize.w, gridSize.h * 24);
      ctx.fillRect(gridSize.w * 22, 0, gridSize.w * 8, gridSize.h);
      ctx.fillRect(gridSize.w * 22, 0, gridSize.w, gridSize.h * 4);
    },
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
      this.render();
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
  mounted() {
    this.render();
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
