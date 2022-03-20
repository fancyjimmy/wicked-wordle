<template>
  <div v-show="isDisplayed" class="backdrop" ref="backdrop">
    <div class="pop-up" ref="pop-up">
      <span v-if="closable" class="close" @click="close">&times;</span>
      <slot> </slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'pop-up',
  props: {
    show: {
      type: Boolean,
      default: true,
    },
    closable: {
      type: Boolean,
      default: true,
    },
    closeByOutisdeClick: {
      type: Boolean,
      default: true,
    },
    autoClose: {
      type: Boolean,
      default: false,
    },
    autoCloseTime: {
      type: Number,
      default: 2000,
    },
  },
  data() {
    return { isDisplayed: this.show };
  },
  computed: {
    displayed: {
      get() {
        return this.isDisplayed;
      },
      set(bool) {
        if (typeof bool !== "boolean") return;

        if (bool) {
          this.isDisplayed = true;

          if (this.autoClose)
            setTimeout(() => (this.displayed = false), this.autoCloseTime);

          if (this.closeByOutisdeClick) {
            this.$refs.backdrop.onclick = (event) => {
              event.preventDefault();

              this.displayed = false;
            };
          }
          this.$emit('opened', '');
          return;
        }

        this.isDisplayed = false;
        this.$emit('closed', '');

        if (this.$refs.backdrop) this.$refs.backdrop.onclick = () => {};
        return;
      },
    },
  },
  methods: {
    display() {
      this.displayed = true;
    },
    close() {
      this.displayed = false;
    },
  },
  created() {
    //this.displayed = this.show;
  },
};
</script>

<style scoped lang="scss">
@keyframes show {
  from {
    opacity: 0;
  }
  to {
    opactiy: 1;
  }
}

.backdrop {
  background: rgba(0 0 0 /0.3);
  position: fixed;
  width: 100vw;
  height: 100vh;
  margin: 0;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  animation: show 0.3s;

}
.pop-up {
  margin: 20px;
  position: relative;
  display: block;
  background: white;
  z-index: 2;

  max-width: 700px;
  padding: 20px;
  border: none;
  border-radius: 5px;
}

.close {
  position: absolute;
  z-index: 1;
  top: 2px;
  right: 2px;

  font-size: 1.5rem;

  border: none;
  width: 20px;
  height: 20px;
  color: #aaaaaa;
}
.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;

  cursor: pointer;
}
</style>
