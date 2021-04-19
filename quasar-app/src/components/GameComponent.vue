<template>
  <div class="game full-width">
    <div class="output">
      <canvas id="gameCanvas"></canvas>
    </div>
    <div class="input">
      <q-btn
        round
        v-for="letter in alphabet"
        v-show="letter.enabled == 1"
        :key="letter.id"
        v-on:click="toggle(letter)"
      >
        {{ letter.value }}
      </q-btn>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
export default Vue.extend({
  name: 'Game',
  props: {
    msg: String,
  },
  data() {
    const alphabet = [
      { value: 'A', enabled: 1 },
      { value: 'B', enabled: 1 },
      { value: 'C', enabled: 1 },
      { value: 'D', enabled: 1 },
      { value: 'B', enabled: 1 },
      { value: 'C', enabled: 1 },
      { value: 'D', enabled: 1 },
      { value: 'E', enabled: 1 },
      { value: 'F', enabled: 1 },
      { value: 'G', enabled: 1 },
      { value: 'H', enabled: 1 },
      { value: 'I', enabled: 1 },
      { value: 'J', enabled: 1 },
      { value: 'K', enabled: 1 },
      { value: 'L', enabled: 1 },
      { value: 'M', enabled: 1 },
      { value: 'N', enabled: 1 },
      { value: 'O', enabled: 1 },
      { value: 'P', enabled: 1 },
      { value: 'Q', enabled: 1 },
      { value: 'R', enabled: 1 },
      { value: 'S', enabled: 1 },
      { value: 'T', enabled: 1 },
      { value: 'U', enabled: 1 },
      { value: 'V', enabled: 1 },
      { value: 'W', enabled: 1 },
      { value: 'X', enabled: 1 },
      { value: 'Y', enabled: 1 },
      { value: 'Z', enabled: 1 },
    ];

    const difficultyLevel = 1;
    return { alphabet, difficultyLevel };
  },
  methods: {
    toggle(button = {value: '', enabled: 0}): void {
      button.enabled = button.enabled == 1 ? 0 : 1;
    },
    word() {
      let word = 'hangman';
      switch (this.difficultyLevel) {
        case 1:
          word = 'dog';
          break;
        case 2:
          word = 'garden';
          break;
        case 3:
          word = 'intrinsic';
          break;
        default:
          word = 'hangman';
          break;
      }
      return word;
    },
    test(letter: JSON) {
      console.log(letter);
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
  padding: 8px 0px;
}
</style>
