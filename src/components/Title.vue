<script>
  export default {
    name: 'Title',
    props: {
      name: String
    },
    data() {
        return {
            msg: "src/assets/Jeopardy_logo.png",
            jeopardyID: "logo"
        }
    },
  }
</script>

<template>
  
  <img id="logo" :src="msg" alt="Jeopardy!">
 
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
  text-align: center;
}

#logo {
  display: flex;
  justify-content: center;
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
