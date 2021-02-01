<template>
  <div class="center">
    <div class=splitflap v-for="(flap, flapIndex) in flapCount" :key="flapIndex">
      <div class="top"></div>
      <div class="bottom"></div>
      <div class="nextHalf"></div>
      <div class="nextFull"></div>
    </div>
  </div>
</template>

<script>
const CHARS = [
  'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L',
  'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X',
  'Y', 'Z', '1', '2', '3', '4', '5', '6', '7', '8', '9', '0', ' '
]

export default {
  name: 'SplitFlapBoard',
  data() {
    return {
      speed: .2, // seconds
      startString: null,
      endString: null,
      flapCount: 0,
      indexOfCharacter: 0,
      topLetters: null,
      bottomLetters: null,
      nextFullLetters: null,
      nextHalfLetters: null,
      doneFlipping: null,
      flag2: null,
    }
  },
  mounted() {
    this.startString = "CHICAGO".toUpperCase().split("")
    this.endString = "NEW YORK".toUpperCase().split("")
    // A-Z, 0-9, spaces only

    // flap count is greater of the length of the two strings
    this.flapCount = (this.startString.length >= this.endString.length) ? this.startString.length : this.endString.length

    this.topLetters = document.getElementsByClassName("top")
    this.bottomLetters = document.getElementsByClassName("bottom")
    this.nextFullLetters = document.getElementsByClassName("nextFull")
    this.nextHalfLetters = document.getElementsByClassName("nextHalf")

    for (let x = 0; x < this.topLetters.length; x++) {
      this.bottomLetters[x].style.animationDuration = this.speed + "s";
      this.nextHalfLetters[x].style.animationDuration = this.speed + "s";
    }

    this.indexOfCharacter = []
    this.doneFlipping = []
    this.doneFlipping.fill(false)

    this.matchStartAndEndArrayLengths()
    this.setIndexOfCharacter()

    setInterval(() => {
      for (let q = 0; q < this.flapCount; q++) {
        console.log(this.nextFullLetters[q])
        if (this.nextFullLetters[q].innerHTML == this.endString[q]) {
          this.dontFlipIt(q)
        }
        else {
          this.flipIt(q)
        }

        if (this.doneFlipping.every((e) => {
          return e
        }) && this.flag2) {
          this.flag2 = false
          this.changeDestination()
        }
      }

    }, this.speed * 1000)
  },
  methods: {
    matchStartAndEndArrayLengths() {
      // match length of startString array and endString array
      if (this.startString.length < this.flapCount) {
        const diff = this.flapCount - this.startString.length
        for (let i = 0; i < diff; i++) {
          this.startString.push(" ")
        }
      } else if (this.endString.length < this.flapCount) {
        const diff = this.flapCount - this.endString.length
        for (let i = 0; i < diff; i++) {
          this.endString.push(" ")
        }
      }
    },
    setIndexOfCharacter() {
      for (let i = 0; i < this.flapCount; i++) {
        this.indexOfCharacter[i] = CHARS.indexOf(this.startString[i])
        this.flag2 = true
      }
    },
    changeDestination() {
      setTimeout(function() {
        this.doneFlipping.fill(false)
        this.flag2 = true
        
        var tempArr = this.endString.slice()
        this.endString = this.startString.slice()
        this.startString = tempArr.slice()
      }, 3000);
    },
    dontFlipIt(x) {
      this.doneFlipping[x] = true
      this.bottomLetters[x].classList.remove("flip2")
      this.bottomLetters[x].style.backgroundColor = "#121212"
      this.nextHalfLetters[x].style.backgroundColor = "#121212"
      this.topLetters[x].innerHTML = CHARS[(this.indexOfCharacter[x] == 0) ? CHARS.length - 1 : this.indexOfCharacter[x] - 1]
      this.bottomLetters[x].innerHTML = CHARS[(this.indexOfCharacter[x] == 0) ? CHARS.length - 1 : this.indexOfCharacter[x] - 1]
    },
    flipIt(x) {
      this.topLetters[x].innerHTML = CHARS[(this.indexOfCharacter[x] == 0) ? CHARS.length - 1 : this.indexOfCharacter[x] - 1]
      this.bottomLetters[x].innerHTML = CHARS[(this.indexOfCharacter[x] == 0) ? CHARS.length - 1 : this.indexOfCharacter[x] - 1]
      this.nextFullLetters[x].innerHTML = CHARS[this.indexOfCharacter[x]]
      this.nextHalfLetters[x].innerHTML = CHARS[this.indexOfCharacter[x]]

      this.bottomLetters[x].classList.remove("flip1")
      // this.bottomLetters[x].offsetWidth = this.bottomLetters[x].offsetWidth;
      this.bottomLetters[x].classList.add("flip1")
      this.nextHalfLetters[x].classList.remove("flip2")
      // this.nextHalfLetters[x].offsetWidth = this.nextHalfLetters[x].offsetWidth;
      this.nextHalfLetters[x].classList.add("flip2")

      if (this.indexOfCharacter[x] > CHARS.length - 2) {
        this.indexOfCharacter[x] = 0
      } else {
        this.indexOfCharacter[x]++
      }
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
body {
  background-color: #2c2c2c;
}

.splitflap {
  position: relative;
  min-width: 100px;
  height: 100px;
  margin: 10px;
  line-height: 100px;
  font-size: 100px;
  font-family: Monospace;
  text-align: center;
  color: white;
}

.center {
  position: absolute;
  left: 0;
  top: 50%;
  margin-top: -50px;
  width: 100%;
  display: flex;
  justify-content: center;
}

.top {
  position: relative;
  height: 50%;
  width: 100%;
  background-color: #121212;
  border-radius: 10px 10px 0 0;
  overflow: hidden;
  z-index: 0;
}

div {
  perspective: 500px;
}

.bottom {
  position: relative;
  height: 100%;
  width: 100%;
  margin-top: -50%;
  border-radius: 10px 10px 10px 10px;
  z-index: -1;
  background-color: black;
  background-image: linear-gradient(rgba(59, 182, 235, 0), #121212);
  transform-origin: center;
}

.nextHalf {
  position: relative;
  height: 50%;
  width: 100%;
  margin-top: -100%;
  overflow: hidden;
  border-radius: 10px 10px 0 0;
  z-index: 2;
  background-color: black;
  background-image: linear-gradient(#121212, rgba(59, 182, 235, 0));
  transform-origin: bottom;
}

.nextFull {
  position: relative;
  height: 100%;
  width: 100%;
  background-color: #121212;
  margin-top: -50%;
  border-radius: 10px 10px 10px 10px;
  z-index: -3;
}

.flip1 {
  animation: flip1 ease-in 1;
  animation-duration: 1s;
}

.flip2 {
  animation: flip2 ease-out 1;
  animation-duration: 1s;
}

@keyframes flip1 {
  0% {
    transform: rotateX(0deg);
    background-color: #121212;
  }
  50% {
    transform: rotateX(90deg);
    background-color: black;
  }
  100% {
    transform: rotateX(90deg);
  }
}

@keyframes flip2 {
  0% {
    transform: rotateX(-90deg);
  }
  50% {
    transform: rotateX(-90deg);
  }
  100% {
    transform: rotateX(0deg);
    background-color: #121212;
  }
}
</style>
