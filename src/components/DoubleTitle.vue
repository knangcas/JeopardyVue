<script>
export default {
  name: 'DoubleTitle',
  data() {
    return {
      logo: "src/assets/double_jeopardy.png",
      jeopardyID: "logo",
    }
  },
}
</script>

<template>
  <div class="grid-container">
    <div class="grid-item">
      <img id="logo" :src="logo" alt="Jeopardy!">
    </div>
  </div>
</template>

<style scoped>
.grid-item{
  grid-column: 1/4;
  font-family: "Arial Rounded MT Bold",serif;
  font-size:1.5rem;
  color:white;
  height:auto;
  justify-content:right;


}


.grid-container {
  display: grid;

  padding: 10px;

  border-radius: 10px

}
.grid-item {
  padding: 20px;
  font-size: 2rem;
  text-align: center;
  color: orange;
  font-family: "Impact",serif;
  font-weight: lighter;
}
</style>
