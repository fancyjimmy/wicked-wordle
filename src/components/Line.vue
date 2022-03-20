<template>
  <div class="line" :style="lines">
    <!--  <div class="letter" v-for="i in length" :key="i">h</div> -->
    <Letter
      v-for="i in length"
      :key="i"
      :letter="guess.substring(i - 1, i)"
      :state="status(i)"
      :finished="finished"
    />
  </div>
</template>

<script>
import Letter from './Letter.vue';

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Line',
  components: {
    Letter,
  },
  props: {
    length: Number,
    word: {
      type: String,
      required: true,
    },
    guess: {
      type: String,
      required: true,
    },
    finished: Boolean,
  },
  data() {
    return {
    };
  },

  computed: {
    values() {
      return [...Array.from(this.guess)];
    },

    lines() {
      return '--lines :' + this.length;
    },
  },
  methods:{
    status(index) {
      let actual = this.word.charAt(index-1);
      let char = this.guess.charAt(index-1);
      //console.log(char);

      if (actual === char) return 'match';
      if (this.word.includes(char)) return 'contain';
    },

  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.line {
  display: grid;
  grid-template-columns: repeat(var(--lines), 1fr);
  width: 100%;
  grid-gap: 5px;

  margin: auto;
  margin-top: 5px;
}
</style>
