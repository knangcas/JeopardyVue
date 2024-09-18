<script>

import Jeopardy from './components/Jeopardy.vue';
import Title from './components/Title.vue';
import DoubleTitle from "@/components/DoubleTitle.vue";

export default {
  data() {
    return {
      doubleJeopardy:false,
    }
  },
  components: {
    DoubleTitle,
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
  <div><Title v-if="!doubleJeopardy" id="topTitle"></Title>
    <DoubleTitle v-if="doubleJeopardy" id="topTitle"></DoubleTitle>
  </div>
  <Jeopardy @doubleJ="(v)=>this.doubleJeopardy=v"></Jeopardy>
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

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
