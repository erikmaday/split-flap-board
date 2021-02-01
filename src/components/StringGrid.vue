<template>
  <div class="table">
    <div class="row" v-for="(row, rowIndex) in startStringCharacterIndexGrid" :key="rowIndex">
        <div class="cell" v-for="(value, columnIndex) in row" :key="`${rowIndex}-${columnIndex}-${value}`">
            <div class="letter">{{ CHARS[startStringCharacterIndexGrid[rowIndex][columnIndex]] }}</div>
            <div v-if="offsetGrid[rowIndex][columnIndex] !== 0" class="flap top-new">
              {{ CHARS[startStringCharacterIndexGrid[rowIndex][columnIndex] + 1] }}
            </div>
            <div v-if="offsetGrid[rowIndex][columnIndex] !== 0" class="flap top-old" :style="`animation-duration: ${speed}s;`">
              {{ CHARS[startStringCharacterIndexGrid[rowIndex][columnIndex]] }}
            </div>
            <div v-if="offsetGrid[rowIndex][columnIndex] !== 0" class="flap bottom-new" :style="`animation-duration: ${speed}s;`">
              {{ CHARS[startStringCharacterIndexGrid[rowIndex][columnIndex] + 1] }}
            </div>
            <div v-if="offsetGrid[rowIndex][columnIndex] !== 0" class="flap bottom-old">
              {{ CHARS[startStringCharacterIndexGrid[rowIndex][columnIndex]] }}
            </div>
            <div class="hinge">
            </div>

        </div>
    </div>
  </div>
</template>

<script>
const CHARS = [
  'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L',
  'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X',
  'Y', 'Z', ' ', '1', '2', '3', '4', '5', '6', '7', '8', '9', '0',
  '\'', '"', ',', '.', '!', ':', ';', '@', '#', '$', '%', '&', '*',
   '(', ')', '_', '-', '+', '=', '/', '',
]

export default {
  props: {
    input: {
      type: String,
      default: ''
    },
    rows: {
      type: Number,
      default: 0,
    },
    columns: {
      type: Number,
      default: 0,
    }
  },
  data() {
    return {
      displayGrid: null,
      startStringCharacterIndexGrid: null,
      endStringCharacterIndexGrid: null,
      offsetGrid: null,
      allTilesUpdated: true,
      iterateTilesInterval: null,
      speed: .1,
      CHARS,
    }
  },
  watch: {
    input(newVal) {
      const endString = newVal
      this.startStringCharacterIndexGrid = this.endStringCharacterIndexGrid
      this.endStringCharacterIndexGrid = this.generateGrids(endString).charIndex
      this.createOffsetGrid()
      this.allTilesUpdated = false
      const totalTiles = this.rows*this.columns
      this.iterateTilesInterval = setInterval(() => {
        this.allTilesUpdated = this.iterate() === totalTiles
      }, this.speed * 1000)
    },
    allTilesUpdated() {
      if (this.allTilesUpdated) {
        clearInterval(this.iterateTilesInterval)
      }
    }
  },
  mounted() {
    this.displayGrid = this.establishGrid(this.rows, this.columns)
    this.endStringCharacterIndexGrid = this.establishGrid(this.rows, this.columns)
    this.offsetGrid = this.establishGrid(this.rows, this.columns, 0)
    const grids = this.generateGrids(this.input)
    this.displayGrid = grids.display
    this.startStringCharacterIndexGrid = grids.charIndex
    this.endStringCharacterIndexGrid = grids.charIndex
  },
  methods: {
    establishGrid(rows, columns, fill) {
      let grid = new Array(rows)
      for (let row = 0; row < rows; row++) {
        grid[row] = new Array(columns)
        if (fill !== undefined) {
          grid[row].fill(fill)
        }
      }
      return grid
    },
    generateGrids(string) {
      let display = this.establishGrid(this.rows, this.columns)
      let charIndex = this.establishGrid(this.rows, this.columns)
      let i = 0
      for (let row = 0; row < this.rows; row++) {
        for (let col = 0; col < this.columns; col++) {
          const char = string.charAt(i++)
          display[row][col] = char
          charIndex[row][col] = CHARS.indexOf(char)
        }
      }
      return { display, charIndex }
    },
    createOffsetGrid() {
      for (let row = 0; row < this.rows; row++) {
        for (let col = 0; col < this.columns; col++) {
          if (['', ' '].includes(this.endStringCharacterIndexGrid[row][col]) && ['', ' '].includes(this.startStringCharacterIndexGrid[row][col])) {
            this.offsetGrid[row][col] = 0
          }
          const diff = this.endStringCharacterIndexGrid[row][col] - this.startStringCharacterIndexGrid[row][col]
          this.offsetGrid[row][col] = diff >= 0 ? diff : diff + CHARS.length
        }
      }
    },
    iterate() {
      let count = 0
      for (let row = 0; row < this.rows; row++) {
        for (let col = 0; col < this.columns; col++) {
          if (this.offsetGrid[row][col] !== 0) {
            this.offsetGrid[row][col]--
            this.startStringCharacterIndexGrid[row][col] < this.CHARS.length - 1 ? this.startStringCharacterIndexGrid[row][col]++ : this.startStringCharacterIndexGrid[row][col] = 0
          } else {
            count++
          }
        }
      }
      this.$forceUpdate()
      return count
    }
  }
}
</script>

<style lang="scss" scoped>
@import '~@/scss/design.scss';

$flap-height: 80px;
$flap-width: 50px;
$flap-font-size: 60px;
$hinge-height: 0.03em;

$size-multiplier: 1.75;

.table {
  display: table;
  border-collapse: separate;
  border-spacing: 10px;
  .row {
    display: table-row;
    .cell {
      display: table-cell;
      position: relative;
      background-color: $black;
      vertical-align: middle;
      text-align: center;
      font-family: 'League Gothic';
      font-size: $flap-font-size * $size-multiplier;
      color: $white;
      padding: 0;
      height: $flap-height * $size-multiplier;
      width: $flap-width * $size-multiplier;
      box-shadow: inset 2px 2px 7px rgba($trueBlack, .25),
                  1px 1px 0px 0px rgba($white, .05);
      .letter {
        position: absolute;
        height: 100%;
        width: 100%;
        left: 0;
        top: 5%;
      }
      .hinge {
        width: 100%;
        position: absolute;
        left: 0;
        top: 50%;
        transform: translateY(-50%);
        z-index: 3;
        box-sizing: border-box;
        height: $hinge-height;
        background-color: inherit;
        box-shadow: none !important;
      }
      .flap {
        position: absolute;
        top: 0%;
        padding-top: 5%;
        height: 100%;
        width: 100%;
        animation-fill-mode: forwards;
        transform-origin: center;
        background-color: inherit;
        background: inherit;
        z-index: 1;
        border-radius: inherit;
        box-sizing: border-box;

        &.top {
          &-old {
            clip-path: polygon(0 50%, 100% 50%, 100% 0, 0 0);
            animation-name: flapDownTop;
            animation-duration: 1s;
            animation-iteration-count: infinite;
            animation-timing-function: ease-in;
            z-index: 2;
          }
          &-new {
            clip-path: polygon(0 50%, 100% 50%, 100% 0, 0 0);
          }
        }

        &.bottom {
          &-new {
            clip-path: polygon(0 100%, 100% 100%, 100% 50%, 0 50%);
            animation-name: flapDownBottom;
            animation-iteration-count: infinite;
            animation-timing-function: ease-out;
            z-index: 2;
          }
          &-old {
            clip-path: polygon(0 100%, 100% 100%, 100% 50%, 0 50%);
          }
        }
      }
    }
  }
}

</style>