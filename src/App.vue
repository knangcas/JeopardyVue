<script>

import Jeopardy from './components/Jeopardy.vue';
import Title from './components/Title.vue';

export default {
  data() {
    return {
      player: 0,
      playerText: "Player "
    }
  },
  components: {
    Title,
    Jeopardy
  }
}
</script>

<template>
  <div><Title id="topTitle"></Title></div><br><div id="bottomText">{{playerText}} {{player}}'s turn.</div><br>
 <Jeopardy @player="(v)=>this.player=v"></Jeopardy>


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
