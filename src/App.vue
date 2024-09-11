<script setup>

import Jepoardy from './components/Jepoardy.vue';
import Title from './components/Title.vue';

</script>

<template>
  <div><Title id="topTitle"></Title></div><br>
 <Jepoardy></Jepoardy>
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
  align-items: center;
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
