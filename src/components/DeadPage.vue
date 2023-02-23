<template>
  <div class="deadPage">
    <h1>V你50不要？</h1>
    <div @click="playAgian()">要</div>
    <div @click="back()">不要了</div>
  </div>
</template>

<script>
export default {
  name: 'deadPage',
  props: {
  },
  methods: {
    playAgian: function () {
      var p = this.$store.state.pass
      console.log(p)
      if (p === '1') {
        this.$router.push('/first')
      } else if (p === '2') {
        this.$router.push('/second')
      }
      window.location.reload()
    },
    back: function () {
      this.$router.push('/')
      window.location.reload()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .deadPage {
    position: absolute;
    top: 50px;
    left: 50%;
    padding-top: 50px;
    transform: translate(-50%,0);
    width: 600px;
    height: 450px;
    text-align: center;
    border: 5px solid black;
    border-radius: 15px;
  }
  .deadPage h1 {
    font-size: 80px;
    font-family: "造字工房韵黑体（非商用）";
  }
  .deadPage div {
    cursor: pointer;
    margin: 50px auto;
    width: 200px;
    font-family: "造字工房韵黑体（非商用）";
    line-height: 60px;
    font-size: 30px;
    height: 60px;
    border: 5px solid black;
    border-radius: 30px;
  }
  .deadPage div:hover {
    background-color: black;
    color: white;
  }
</style>
