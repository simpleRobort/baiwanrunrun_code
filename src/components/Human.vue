<template>
 <div class="human" :style="{left:Hleft,bottom:Hbottom}" id="human">
   <img :src="humanPic" alt="">
 </div>
</template>

<script>
export default {
  name: 'Human',
  data () {
    return {
      Hleft: '20px',
      Hbottom: '187.61px',
      humanPic: require('@/assets/human/quite1.0.1.gif'),
      isDead: false
    }
  },
  mounted () {
    this.moveOpen()
  },
  methods: {
    moveOpen: function () {
      const that = this
      document.onkeydown = function () {
        if (that.isDead) return
        const key = window.event.keyCode
        switch (key) {
          case 65:
            // A
            that.goLeft(0)
            break
          case 68:
            // D
            that.goRight(0)
            break
          case 87:
          case 32:
            // W
            that.jump(8)
            break
        }
      }
      document.onkeyup = function () {
        const key = window.event.keyCode
        switch (key) {
          case 65:
            // A
            that.goLeft(1)
            break
          case 68:
            // D
            that.goRight(1)
            break
        }
      }
      this.moveClock()
    },
    goLeft: function (type) {
      if (type === 0) {
        this.changeStatus('isLeft', true)
      } else if (type === 1) {
        this.changeStatus('isLeft', false)
      }
    },
    goRight: function (type) {
      if (type === 0) {
        this.changeStatus('isRight', true)
      } else if (type === 1) {
        this.changeStatus('isRight', false)
      }
    },
    jump: function (num) {
      if (this.isDead) return
      if (this.$store.state.humanInfo.isJump) return
      this.changeStatus('isJump', true)
      var that = this
      var speed = num
      function jp () {
        const jpheight = parseFloat(that.Hbottom.replace('px', ''))
        if (speed > 0) {
          if (that.$store.getters.cantJump(true, jpheight, parseFloat(that.Hleft.replace('px', '')), 'stopArea')) {
            speed = 0
          }
          if (that.$store.getters.cantJump(true, jpheight, parseFloat(that.Hleft.replace('px', '')), 'deadArea')) {
            that.isDead = true
            that.humanPic = require('@/assets/human/dead.gif')
            return setTimeout(function () {
              that.$router.push('/dead')
            }, 2000)
          }
          if (that.$store.getters.cantJump(true, jpheight, parseFloat(that.Hleft.replace('px', '')), 'winArea')) {
            return that.$router.push('/win')
          }
        } else {
          if (that.$store.getters.cantJump(false, jpheight, parseFloat(that.Hleft.replace('px', '')), 'stopArea')) {
            that.humanPic = require('@/assets/human/quite1.0.1.gif')
            return that.changeStatus('isJump', false)
          }
          if (that.$store.getters.cantJump(false, jpheight, parseFloat(that.Hleft.replace('px', '')), 'deadArea')) {
            that.isDead = true
            that.humanPic = require('../assets/human/dead.gif')
            return setTimeout(function () {
              that.$router.push('/dead')
            }, 2000)
          }
          if (that.$store.getters.cantJump(false, jpheight, parseFloat(that.Hleft.replace('px', '')), 'winArea')) {
            return that.$router.push('/win')
          }
        }
        that.Hbottom = jpheight + speed + 'px'
        if (jpheight < 187.61) {
          that.Hbottom = '187.61px'
          that.$store.commit('changestayIndex', -1)
          that.humanPic = require('@/assets/human/quite1.0.1.gif')
          that.changeStatus('isJump', false)
          that.changeStatus('isJump', false)
        } else {
          that.humanPic = require('@/assets/human/jump1.0.1.gif')
          speed -= 0.2
          setTimeout(jp, 10)
        }
      }
      jp()
    },
    changeStatus: function (name, value) {
      const info = {
        name,
        value
      }
      this.$store.commit('changeStatus', info)
    },
    moveClock: function () {
      var that = this
      var human = document.getElementById('human')
      setInterval(function () {
        if (that.isDead) return
        var Hleft = parseFloat(that.Hleft.replace('px', ''))
        var Hbottom = parseFloat(that.Hbottom.replace('px', ''))
        if (that.$store.state.humanInfo.isLeft) {
          if (human.offsetLeft <= 0) {
            that.Hleft = '0px'
          } else {
            if (that.$store.getters.cantgo(Hleft - 5, Hbottom, 'stopArea')) return
            if (that.$store.getters.cantgo(Hleft - 5, Hbottom, 'deadArea')) {
              that.isDead = true
              that.humanPic = require('@/assets/human/dead.gif')
              return setTimeout(function () {
                that.$router.push('/dead')
              }, 2000)
            }
            if (that.$store.getters.cantgo(Hleft - 5, Hbottom, 'winArea')) {
              return that.$router.push('/win')
            }
            if (that.$store.state.stayIndex !== -1) {
              if (that.$store.getters.freeFall(Hleft)) {
                that.jump(0)
              }
            }
            that.Hleft = parseFloat(that.Hleft.replace('px', '')) - 2.5 + 'px'
          }
        } else if (that.$store.state.humanInfo.isRight) {
          if ((human.offsetLeft + 15) >= 1280) {
            that.Hleft = '1265px'
          } else {
            if (that.$store.getters.cantgo(Hleft + 15, Hbottom, 'stopArea')) return
            if (that.$store.getters.cantgo(Hleft + 15, Hbottom, 'deadArea')) {
              that.isDead = true
              that.humanPic = require('@/assets/human/dead.gif')
              return setTimeout(function () {
                that.$router.push('/dead')
              }, 2000)
            }
            if (that.$store.getters.cantgo(Hleft + 15, Hbottom, 'winArea')) {
              return that.$router.push('/win')
            }
            if (that.$store.state.stayIndex !== -1) {
              if (that.$store.getters.freeFall(Hleft)) {
                that.jump(0)
              }
            }
            that.Hleft = Hleft + 2.5 + 'px'
          }
        }
      }, 10)
    }
  }
}
</script>

<style scoped>
  .human {
    position: absolute;
    width: 15px;
    height: 60px;
    /*background-image: url("../assets/human/quite.gif");*/
  }
  .human img {
    position: absolute;
    bottom: 0;
    left: -23px;
    width: 60px;
    height: 70px;
  }
</style>
