<script>

import Jeopardy from './components/Jeopardy.vue';
import Title from './components/Title.vue';

export default {
  data() {
    return {
      player: 0,
      playerText: "Player ",
      playerMoney: []
    }
  },
  components: {
    Title,
    Jeopardy
  },
  methods: {
    dynamicPlayer() {
      return "player" + this.player;
    }
  }
}
</script>

<template>

  <div><Title id="topTitle"></Title></div><div id="bottomText"><!--{{playerText}} {{player}}'s turn.--></div>
<!--  Player 1: ${{playerMoney[0]}} <br>
  Player 2: ${{playerMoney[1]}} <br>
  Player 3: ${{playerMoney[2]}} <br>-->
 <Jeopardy></Jeopardy>


  </template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

#topTitle {
  display: flex; justify-content: center;
}
#bottomText {
  display: flex; justify-content: left;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
