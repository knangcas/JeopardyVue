<script>
  export default {
    name: 'Title',
    props: {
      name: String
    },
    data() {
        return {
            msg: "J  E  O  P  A  R  D  Y",
            amt: 25
        }
    },
  }
</script>

<template>
  
  <h1 class="titleTop">{{ msg }}</h1>
 
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
  text-align: center;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
