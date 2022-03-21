<template>
  <div class="guess" :class="status">
    {{ letter }}
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'letter',
  props: {
    letter: String,
    state: String,
    finished: Boolean,
  },
  computed: {
    status() {
      return {
        empty: this.letter === '',
        contains: this.state === 'contain' && this.finished,
        match: this.state === 'match' && this.finished,
        finished: this.finished,
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@keyframes blimp {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
  100% {
    transform: scale(1);
  }
}
$bg-time: 0.3s;
$rotate-time: 0.6s;
.guess {
  width: 100%;

  display: inline-flex;
  justify-content: center;
  align-items: center;

  background: #3a3a3c;
  border-radius: 3px;
  color: white;

  font-size: 1.8em;
  line-height: 1.8em;
  font-weight: bold;
  vertical-align: middle;
  text-transform: uppercase;
  user-select: none;
  box-sizing: border-box;

  transition: background $bg-time, font-size $bg-time, transform $rotate-time;

  transform-style: preserve-3d;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
}

.guess::before {
  content: '';
  display: inline-block;
  padding-bottom: 100%;
}

.contains {
  background: #d2b739;
}

.match {
  background: #538d4e;
}

.finished {
  animation: blimp $rotate-time;
}

.empty {
  border: 2px solid #3a3a3c;
  transform: rotateY(180deg);

  background: inherit;

  transition: background $bg-time, transform $rotate-time;
  transform-style: preserve-3d;
}
</style>
