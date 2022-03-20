<template>
  <div class="board-wrapper">
    <div class="board">
      <div v-for="index in maxGuesses" :key="index">
        <Line
            :length="wordLength"
            :word="word"
            :guess="lowerGuesses[index - 1]"
            :finished="isCurrent(index)"
        />
      </div>
    </div>
  </div>

  <div class="keyboard-wrapper">
    <Keyboard
        @write="write"
        :used="usedChars"
        :contained="containedChars"
        :matching="matchingChars"
    />
  </div>

  <PopUp :show="false" :autoClose="false" ref="retry" :closable="false" :closeByOutisdeClick="false">
    <div>
      <p>Want to try Again?</p>
      <div
          @click="reset"
          style="
          font-weight: 800;
          font-size: 1.5rem;
          margin: auto;
          display: flex;
          align-items: center;
          justify-content: center;
          user-select: none;
        "
      >
        ↺
      </div>
    </div>
  </PopUp>

  <PopUp
      :show="false"
      ref="invalid"
      :closable="true"
      :autoClose="true"
      :autoCloseTime="2000"
  >
    <p>Answer was invalid</p>
  </PopUp>

  <PopUp :show="false" ref="failed" :closable="true" @closed="showRetry">
    <p>
      Sorry your Guesses were wrong. The right answer would have been
      <span style="font-weight: 800; text-transform: uppercase">
        {{ word }}...</span
      >
    </p>
  </PopUp>

  <PopUp :show="false" ref="won" :closable="true" @closed="showRetry">
    <p>
      Correct Answer!!!!
      <span style="font-weight: 800; text-transform: uppercase">
        {{ word }}...</span
      >
    </p>
  </PopUp>

  <!--
    <Modal
      :show="false"
      ref="retry"
      :closable="false"
      :closeByOutisdeClick="false"
    >
      <div>
        <p>Want to try Again?</p>
        <div
          @click="reset"
          style="
            font-weight: 800;
            font-size: 1.5rem;
            margin: auto;
            display: flex;
            align-items: center;
            justify-content: center;
          "
        >
          ↺
        </div>
      </div>
    </Modal>

    <Modal
      :show="false"
      ref="invalid"
      :closable="true"
      :autoClose="true"
      :autoCloseTime="2000"
    >
      <p>Answer was invalid</p>
    </Modal>

    <Modal :show="false" ref="failed" :closable="true" :afterClose="showRetry">
      <p>
        Sorry your Guesses were wrong. The right answer would have been
        <span style="font-weight: 800; text-transform: uppercase">
          {{ word }}...</span
        >
      </p>
    </Modal>
    -->
</template>

<script>
import Line from './components/Line.vue';
import Keyboard from './components/Keyboard.vue';
import PopUp from './components/PopUp.vue';
import wordList from './wordList.js';

export default {
  name: 'App',
  components: {
    Line,
    Keyboard,
    PopUp,
  },
  data() {
    return {
      ended: false,
      currentLine: 0,
      word: '',
      currentGuess: '',
      wordLength: 5,
      maxGuesses: 6,
      guesses: ['', '', '', '', '', ''],
    };
  },
  methods: {
    submit () {
      console.log(this.$refs);

      if (
          this.currentGuess.length != this.wordLength ||
          !wordList.words.includes(this.currentGuess)
      ) {
        this.$refs.invalid.display();
        return;
      }



      if (this.word === this.lowerGuesses[this.currentLine]) {
        this.currentLine++;
        this.currentGuess = '';

        this.$refs.won.display();

        this.ended = true;
        return;
      }

      if (this.currentLine + 1 >= this.maxGuesses) {
        this.ended = true;
        this.$refs.failed.display();
      }

      this.currentGuess = '';
      this.currentLine++;
    },
    isCurrent(index) {
      return index <= this.currentLine;
    },
    write(char) {
      if (this.ended) return;
      char = char.toLowerCase();

      if (char === 'enter') {
        this.submit();
        this.updateLine();
        return;
      }

      if (char === '⇦' || char === 'backspace') {
        this.currentGuess = this.currentGuess.slice(0, -1);
        this.updateLine();
        return;
      }

      if (this.currentGuess.length >= this.wordLength) return;

      if (!Array.from('qwertzuiopasdfghjklyxcvbnm').includes(char)) {
        return;
      }

      this.currentGuess += char;
      this.updateLine();
    },
    getRandomWord() {
      let random = Math.random() * wordList.words.length;
      return wordList.words[Math.floor(random)];
    },
    reset() {
      // eslint-disable-next-line no-unused-vars
      this.guesses = this.guesses.map((_) => '');
      this.currentLine = 0;
      this.word = this.getRandomWord();
      this.ended = false;
      this.$refs.retry.close();
    },
    updateLine() {
      this.guesses[this.currentLine] = this.currentGuess;
    },
    showRetry() {
      setTimeout(() =>{
        this.$refs.retry.display();

      }, 1000);
    },
  },
  computed: {
    lowerGuesses() {
      return this.guesses.map((item) => item.toLowerCase());
    },
    usedChars() {
      let a = [];
      this.lowerGuesses
          .slice(0, this.currentLine)
          .forEach((item) => (a = [...a, ...Array.from(item)]));
      return [...new Set(a)];
    },
    containedChars() {
      let a = [];
      this.usedChars.forEach((item) => {
        if (this.word.includes(item)) a.push(item);
      });
      return [...new Set(a)];
    },
    matchingChars() {
      let a = [];
      this.lowerGuesses.slice(0, this.currentLine).forEach((item) => {
        for (let i = 0; i < this.wordLength; i++) {
          let actual = this.word[i];
          let char = item[i];
          //console.log(char);

          if (actual === char) a.push(char);
        }
      });
      return [...new Set(a)];
    },
  },
  created() {
    this.word = this.getRandomWord();
    document.body.addEventListener('keydown', (event) => {
      this.write(event.key.toLowerCase());
    });
  },
};
</script>

<style>
body {
  margin: 0;
  width: 100vw;
  height: 100vh;
}

.board {
  width: 85%;
  max-width: 350px;
  margin: 0 auto;
}

.board-wrapper {
  width: 100%;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 10px;
  flex: 4;
}
.keyboard-wrapper {
  width: calc(100% - 20px);
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 10px 10px;
  flex: 1;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 100%;
  height: 100%;
  font-size: 14px;

  margin: 0;

  background: #121213;
  display: flex;
  flex-direction: column;
}
</style>
