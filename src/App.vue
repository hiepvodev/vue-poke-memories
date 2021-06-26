<template>
  <main-screen
    v-if="statusMatch === 'default'"
    @onStart="onHandleBeforeStart($event)"
  />
  <interact-screen
    v-if="statusMatch === 'match'"
    :cardsContext="setting.cardContext"
    @onFinish="onHandleFinish"
  />
  <result-screen
    v-if="statusMatch === 'finish'"
    @onStart="onHandleStart"
    :timer="timer"
  />
</template>

<script>
import InteractScreen from "./components/InteractScreen.vue";
import MainScreen from "./components/MainScreen.vue";
import ResultScreen from "./components/ResultScreen.vue";
import { shuffled } from "./utils/array";

export default {
  name: "App",
  data() {
    return {
      setting: {
        totalOfBlocks: 0,
        cardContext: [],
        statedAt: null,
      },
      statusMatch: "default",
      timer: null,
    };
  },
  components: {
    MainScreen,
    InteractScreen,
    ResultScreen,
  },
  methods: {
    onHandleBeforeStart(config) {
      this.setting.totalOfBlocks = config.totalOfBlocks;

      const firstCards = Array.from(
        { length: this.setting.totalOfBlocks / 2 },
        (_, i) => i + 1
      );

      const secondCards = [...firstCards];
      const cards = [...firstCards, ...secondCards];
      this.setting.cardContext = shuffled(shuffled(shuffled(shuffled(cards))));
      this.setting.statedAt = new Date().getTime();
      this.statusMatch = "match";
      // console.log(this.setting.cardContext);
    },
    onHandleFinish() {
      this.timer = new Date().getTime() - this.setting.statedAt;
      this.statusMatch = "finish";
    },
    onHandleStart() {
      this.statusMatch = "default";
    },
  },
};
</script>
