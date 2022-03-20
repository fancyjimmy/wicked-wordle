<template>
  <div class="keyboard">
    <div class="row" v-for="row in rows" :key="row">
      <button
        class="letter"
        v-for="letter in row"
        :key="letter"
        @click="write(letter)"
        :class="status(letter)"
      >
        {{ letter }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'key-board',
  props: {
    used: Array,
    contained: Array,
    matching: Array,
  },
  methods: {
    write(char) {
      this.$emit('write', char);
    },
    status(letter) {
      return {
        used: this.used.includes(letter),
        contained: this.contained.includes(letter),
        matching: this.matching.includes(letter),
      };
    },
  },
  data() {
    return {
      rows: [
        Array.from('qwertyuiop'),
        Array.from('asdfghjkl'),
        ['enter', ...Array.from('zxcvbnm'), '⇦'],
      ],
    };
  },
  created() {},
  computed() {
    return {
      row1: () => {
        return {};
      },
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.letter {
  display: block;
  border: none;
  height: 100%;
  width: 100%;

  font-size: 2rem;

  display: inline-flex;
  justify-content: center;
  align-items: center;

  background: #818384;
  border-radius: 3px;
  color: white;

  font-size: 1rem;
  font-weight: bold;
  vertical-align: middle;
  text-transform: uppercase;
  user-select: none;
  cursor: pointer;
  transition: background 0.15s;
}
.keyboard {
  max-width: 500px;
  margin: auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;

  margin-bottom: 5px;

  justify-content: center;

  .row {
    display: grid;
    grid-gap: 5px;
    margin-top: 5px;
    height: 3.6em;
    width: 100%;
  }

  .row:nth-child(1) {
    grid-template-columns: repeat(10, 1fr);
  }

  .row:nth-child(2) {
    grid-template-columns: repeat(9, 1fr);
  }

  .row:nth-child(3) {
    grid-template-columns: repeat(9, 1fr);

    $special-width: 4.5em;

    .letter:nth-child(1) {
      width: $special-width;
    }

    .letter:last-child {
      width: $special-width;
    }
  }
}

.used {
  background: #3a3a3c;
}

.contained {
  background: #b59f3b;
}

.matching {
  background: #538d4e;
}
</style>
