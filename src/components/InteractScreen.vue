<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-flip
        v-for="(card, index) in cardsContext"
        :key="index"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index, card }"
        @onFlip="onHandleFlipCard($event)"
        :ref="`card-${index}`"
        :rules="rules"
        :cardsContext="cardsContext"
      />
    </div>
  </div>
</template>

<script>
import CardFlip from "./Card.vue";
export default {
  components: {
    CardFlip,
  },
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  data() {
    return {
      rules: [],
    };
  },
  methods: {
    onHandleFlipCard(card) {
      if (this.rules.length === 2) return false;

      this.rules.push(card);

      if (
        this.rules.length === 2 &&
        this.rules[0].card === this.rules[1].card
      ) {
        this.$refs[`card-${this.rules[0].index}`].onEnableDisableMode();
        this.$refs[`card-${this.rules[1].index}`].onEnableDisableMode();
        this.rules = [];

        const disabledElements = document.querySelectorAll(
          ".screen .card.disabled"
        );
        console.log(disabledElements.length, this.cardsContext.length);
        if (
          disabledElements &&
          disabledElements.length === this.cardsContext.length - 2
        )
          setTimeout(() => {
            this.$emit("onFinish");
          }, 920);
      } else if (
        this.rules.length === 2 &&
        this.rules[0].card !== this.rules[1].card
      ) {
        //close card
        setTimeout(() => {
          this.$refs[`card-${this.rules[0].index}`].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`].onFlipBackCard();
          this.rules = [];
        }, 800);
      } else {
        return false;
      }
    },
  },
};
</script>

<style scroped>
.screen {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}
.screen__inner {
  /* width: calc(424px); */
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
