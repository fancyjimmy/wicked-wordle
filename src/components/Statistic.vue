<template>
  <div class="statistic">
    <h3>Statistic</h3>

    <div class="stat-container">
      <div>
        <h4 class="stat">Played</h4>
        <h4 class="statvalue">{{ played }}</h4>
      </div>
      <div>
        <h4 class="stat">Win %</h4>
        <h4 class="statvalue">{{ ratio }} %</h4>
      </div>
      <div>
        <h4 class="stat">Current Streak</h4>
        <h4 class="statvalue">{{ currentStreak }}</h4>
      </div>
      <div>
        <h4 class="stat">Max Streak</h4>
        <h4 class="statvalue">{{ maxStreak }}</h4>
      </div>
    </div>
    <div class="bar-container">
      <Bar
        v-for="bar in bars"
        :key="bar"
        :value="bar.total"
        :maxValue="most"
        :name="bar.name"
      />
    </div>
  </div>
</template>

<script>
import Bar from './Bar.vue';

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Statistic',
  components: {
    Bar,
  },
  props: {
    maxGuesses: {
      type: Number,
      default: 6,
    },
  },
  data() {
    return {
      played: 0,
      won: 0,
      distribution: {},
      currentWin: 0,
      currentStreak: 0,
      maxStreak: 0,
    };
  },
  methods: {
    load() {
      const prefix = 'wordle-';
      this.played = window.localStorage.getItem(prefix + 'played') || 0;
      this.won = window.localStorage.getItem(prefix + 'won') || 0;
      this.distribution = JSON.parse(
        window.localStorage.getItem(prefix + 'dist') || '{}'
      );
      this.currentWin = window.localStorage.getItem(prefix + 'curr') || 0;
      this.currentStreak =
        window.localStorage.getItem(prefix + 'curr-streak') || 0;
      this.maxStreak = window.localStorage.getItem(prefix + 'max-streak') || 0;
      console.log(window.localStorage);
    },
    save() {
      const prefix = 'wordle-';
      window.localStorage.setItem(prefix + 'played', this.played);
      window.localStorage.setItem(prefix + 'won', this.won);
      window.localStorage.setItem(
        prefix + 'dist',
        JSON.stringify(this.distribution)
      );
      window.localStorage.setItem(prefix + 'curr', this.currentWin);
      window.localStorage.setItem(prefix + 'curr-streak', this.currentStreak);
      window.localStorage.setItem(prefix + 'max-streak', this.maxStreak);
    },
    wonWithXTurns(turn) {
      this.played++;
      this.won++;
      this.distribution['' + turn] = this.distribution['' + turn] || 0;
      this.distribution['' + turn] = this.distribution['' + turn] + 1;
      this.currentStreak++;
      this.currentWin = turn;
      if (this.currentStreak > this.maxStreak)
        this.maxStreak = this.currentStreak;

      this.save();
    },
    lost() {
      this.played++;
      this.distribution['L'] = (this.distribution['L'] || 0) + 1;
      this.currentStreak = 0;
      this.currentWin = -1;

      this.save();
    },
  },
  computed: {
    ratio() {
      return this.played == 0 ? 0 : Math.round(this.won / this.played) * 100;
    },
    most() {
      let max = 0;
      for (let value of Object.values(this.distribution)) {
        if (value > max) max = value;
      }
      return max;
    },
    bars() {
      let bars = [];
      for (let i = 0; i < this.maxGuesses; i++) {
        let obj = {
          name: i + 1,
          total: this.distribution['' + (i + 1)] || 0,
        };
        bars.push(obj);
      }

      bars.push({
        name: 'L',
        total: this.distribution['L'] || 0,
      });
      return bars;
    },
  },
  created() {
    this.load();
  },
};
</script>

<style lang="scss" scoped>
.statistic {
  display: flex;
  flex-direction: column;
  max-width: 800px;
  align-items: center;
}

.bar-container {
  display: flex;
  flex-direction: column;
}

.stat-container {
  display: grid;
  grid-template-columns: repeat(4, 1fr);

  .stat {
    text-align: center;
    padding: 0;
    margin: 0;
  }

  .statvalue {
    text-align: center;
    margin-top: 4px;
  }

  div {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
  }
}
</style>
