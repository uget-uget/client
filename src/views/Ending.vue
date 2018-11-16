<template>
  <div>
    <div class="win" v-if = "win">
      <h1>Congrats! The other player did worse than you</h1>
      <h2>your final score: {{endScore}}</h2>
      <img src="https://media.giphy.com/media/vvW9GuxsGM4lcjqypz/giphy.gif" alt="snakegif">
    </div>
    <div class="lose" v-else>
      <h1>You lost. Did you even try?</h1>
      <h2>your final score: {{endScore}}</h2>
      <img src="https://media1.tenor.com/images/cf63da2d1dc7eb847d2fb7bcff3b9b00/tenor.gif" alt="snakegif">
    </div>
  </div>
</template>

<script>
export default {
  name: 'ending',
  props: ['endScore'],
  data () {
    return {
      win: false
    }
  }
}
</script>

<style scoped>
  img {
    min-width:500px;
    min-height:500px
  }
  h1, h2 {
    background-color: white
  }
</style>

