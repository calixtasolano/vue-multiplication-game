<script>
import { buildDirectiveArgs } from "@vue/compiler-core";
import GridSquare from "./components/GridSquare.vue";

//Handle mobile view, which requires a smaller game grid
let colDim, rowDim;
const mediaQuery = window.matchMedia("(min-width: 768px)");

function handleTabletChange(e) {
  // Check if the media query is true
  if (e.matches) {
    colDim = 8;
    rowDim = 8;
  } else {
    colDim = 3;
    rowDim = 5;
  }
}

// Initial check
handleTabletChange(mediaQuery);

mediaQuery.addEventListener("change", () => {
  this.handleTabletChange();
});

export default {
  name: "App",
  components: { GridSquare },
  data() {
    return {
      id: 0,
      columnFactors: [],
      rowFactors: [],
      products: [],
      columnFactorVisibility: [],
      rowFactorVisibility: [],
      productsVisibility: [],
      answerArr: [],
      emptyBoxes: true,
      inputCount: 0,
      trueCount: 0,
      falseCount: 0,
      btnActive: false,
      gameOver: false,
      correctFlip: "",
      correctFade: "",
      inputMaxTest: 0,
    };
  },
  computed: {
    //dymnamic grid layout for column headers
    gridStyleColumns() {
      let colNum = this.columnFactors.length + 1;
      return {
        "grid-template-columns": "repeat(" + colNum + ", 1fr)",
      };
    },
    //dymnamic grid layout for products
    gridStyleProducts() {
      let colNum = this.columnFactors.length;
      return {
        "grid-template-columns": "repeat(" + colNum + ", 1fr)",
      };
    },
    totalInputs() {
      //Concat all visibility arrays and get a count of all false (means input is showing)
      let visArr;
      visArr = this.columnFactorVisibility.concat(
        this.rowFactorVisibility,
        this.productsVisibility
      );
      let inputTotal = visArr.filter((value) => value === false).length;
      //Get total of inputs on grid
      return inputTotal;
    },
  },
  methods: {
    buildFactorArray: function (val) {
      //Build column and row factors
      let arr = [];
      //Get random length for array (random integer between 2 and colDim or rowDim) -- define dimensions of grid
      let randomDimensionTwoOrGreater = this.getRandomIntInclusive(2, val);
      do {
        //Generate random int for new factor
        let newFactor = this.getRandomIntInclusive(1, 9);

        //Ensure that the factor isn't duplicated
        if (arr.indexOf(newFactor) === -1) {
          arr.push(newFactor);
        }
      } while (arr.length < randomDimensionTwoOrGreater);

      //Generate id for each factor
      arr = this.addIds(arr);
      return arr;
    },
    //Increment to generate id for each box (factor)
    buildId: function () {
      this.id = this.id + 1;
      return this.id;
    },
    addIds: function (array) {
      let idValuesArray = [];
      for (let i = 0; i < array.length; i++) {
        let obj = {};
        obj.factor = array[i];
        obj.id = this.buildId();
        idValuesArray.push(obj);
      }
      //Array of factors with associated ids
      return idValuesArray;
    },
    //Initialize game grid
    init: function () {
      this.inputMaxTest = 0;
      this.columnFactors = this.buildFactorArray(colDim);
      this.rowFactors = this.buildFactorArray(rowDim);
      this.products = this.buildProductArray();
      this.columnFactorVisibility = this.fiftyFiftyHideShow(
        this.columnFactors.length
      );
      this.rowFactorVisibility = this.fiftyFiftyHideShow(
        this.rowFactors.length
      );
      this.productsVisibility = this.productsHideShow(this.products.length);
      this.inputCount = this.totalInputs;
      this.answerArr = [];
      this.gameOver = false;
      this.emptyBoxes = true;
      this.correctFlip = "";
      this.correctFade = "";
    },
    buildProductArray: function () {
      let arr = [];

      //For each row, calculate the product down the row (across the column headers)
      for (let i = 0; i <= this.rowFactors.length - 1; i++) {
        for (let j = 0; j <= this.columnFactors.length - 1; j++) {
          let product =
            this.rowFactors[i].factor * this.columnFactors[j].factor;
          arr.push(product);
        }
      }

      //Generate id for each factor
      arr = this.addIds(arr);
      return arr;
    },
    //Random hide/show function
    fiftyFiftyHideShow: function (arrLength) {
      let arr = [];
      for (let index = 0; index < arrLength; index++) {
        if (Math.random() < 0.5) {
          arr.push(true);
        } else {
          arr.push(false);
          this.inputMaxTest += 1;
        }
      }
      return arr;
    },
    getRandomIntInclusive: function (min, max) {
      min = Math.ceil(min);
      max = Math.floor(max);
      return Math.floor(Math.random() * (max - min + 1) + min); // The maximum is inclusive and the minimum is inclusive
    },
    productsHideShow: function (arrLength) {
      // take each row one at a time and pick one random index
      //set that index to show
      let productLength = this.products.length;
      let columnLength = this.columnFactors.length;
      let rowLength = this.rowFactors.length;

      let randomSetToShowInProducts = [];

      //Run the random hide/show for the length of the products array
      randomSetToShowInProducts = this.fiftyFiftyHideShow(arrLength);

      //Need to be sure at least one number is showing in each row
      //For each row...
      let randomFromRow;
      for (
        let startIndex = 0;
        startIndex < productLength;
        startIndex = startIndex + columnLength
      ) {
        //take each row...
        let rowSet = randomSetToShowInProducts.slice(
          startIndex,
          startIndex + columnLength
        );
        //If row doesn't include one true, pick a random entry from the row and set it to true.
        if (!rowSet.includes(true)) {
          //Get random index using getRandomIntInclusive(min, max);
          randomFromRow = this.getRandomIntInclusive(
            startIndex,
            startIndex + columnLength - 1
          );
          this.inputMaxTest -= 1;
        }
        //Change the random indices chosen to appear in each row to now read true in visibility
        randomSetToShowInProducts[randomFromRow] = true;
      }

      //Need to be sure at least one number is showing in each column
      //Get indices of all true entries in randomSetToShowInProducts
      let indexesOfTrue = [];
      let arrayOfMods;
      for (let i = 0; i < randomSetToShowInProducts.length; i++) {
        if (randomSetToShowInProducts[i] === true) {
          indexesOfTrue.push(i);
        }
        //Get array of mods from indices to check all are covered
        arrayOfMods = indexesOfTrue.map((x) => x % columnLength);
      }

      for (let i = 0; i < columnLength; i++) {
        //If array of mods doesn't cover all columns, pick a random row number, add mod and set that spot to be true.
        if (!arrayOfMods.includes(i)) {
          let randomFromColumn =
            columnLength * this.getRandomIntInclusive(0, rowLength - 1) + i;
          randomSetToShowInProducts[randomFromColumn] = true;
          this.inputMaxTest -= 1;
        }
      }
      let indexes = this.getAllIndexes(randomSetToShowInProducts, true);

      //Fix the case where there is only one T showing in a row and col. Only one in the cross.
      let idxOnlyTrueInRowCol = this.fixIndexOnlyTRowCol(indexes, columnLength);

      //Check that there are not more inputs than product array length. If there are, ammend colFactors/rowFactors
      let ammendmentsToMake = this.inputMaxTest - arrLength;
      if (ammendmentsToMake > 0) {
        this.ammendVisibility(ammendmentsToMake);
      }

      //Return ammended visibility array for products -- ensures at least 1 number showing per row and column
      return randomSetToShowInProducts;
    },
    getAllIndexes: function (arr, val) {
      var indexes = [],
        i = -1;
      while ((i = arr.indexOf(val, i + 1)) != -1) {
        indexes.push(i);
      }
      return indexes;
    },
    fixIndexOnlyTRowCol: function (arr, numCols) {
      let tempArr = [];
      if (Math.floor(arr[0] / numCols) != Math.floor(arr[1] / numCols)) {
        tempArr.push(arr[0]);
      }
      for (let i = 1; i < arr.length - 1; i++) {
        if (
          Math.floor(arr[i - 1] / numCols) != Math.floor(arr[i] / numCols) &&
          Math.floor(arr[i] / numCols) != Math.floor(arr[i + 1] / numCols)
        ) {
          tempArr.push(arr[i]);
        }
      }
      if (
        Math.floor(arr[arr.length - 2] / numCols) !=
        Math.floor(arr[arr.length - 1] / numCols)
      ) {
        tempArr.push(arr[arr.length - 1]);
      }
      //now have all indexes of only T in row

      let colArr = arr.map((e) => e % numCols); //col num for each index
      for (let i = 0; i < colArr.length; i++) {
        for (let j = 0; j < tempArr.length; j++) {
          let colMod = tempArr[j] % numCols;
          if (colArr[i] == colMod && arr[i] !== tempArr[j]) {
            tempArr.splice(j, 1);
            j--;
          }
        }
      }

      //Show row factor if nothing is showing in the cross.
      for (let j = 0; j < tempArr.length; j++) {
        let colNum = tempArr[j] % numCols;
        let rowNum = Math.floor(tempArr[j] / numCols);
        if (this.columnFactorVisibility[colNum] == false) {
          this.rowFactorVisibility[rowNum] = true;
          this.inputMaxTest -= 1;
        }
      }
    },
    ammendVisibility: function (numChanges) {
      for (let i = 0; i < numChanges; i++) {
        let indexC = this.columnFactorVisibility.indexOf(false);
        this.columnFactorVisibility[indexC] = true;
        i++;
        if (i < numChanges) {
          let indexR = this.rowFactorVisibility.indexOf(false);
          this.rowFactorVisibility[indexR] = true;
        }
      }
    },
    //Check button toggles the empty box color and checks if done with game
    showResult: function (event) {
      this.emptyBoxes = !this.emptyBoxes;

      if (this.inputCount === this.trueCount) {
        //alert("gameOver");
        this.gameOver = !this.gameOver;
        this.correctFlip = "flip";
        this.correctFade = "fade";
      }
    },
    eventHandler(data) {
      //Collect inputs and process true/false in answerArr

      //First, check if the id already exists in the answer array. If not, add the id.
      const index = this.answerArr.findIndex((object) => {
        return object.id == data.item.id;
      });
      //If it's already in answer array, and input is true, assign true to the corresponding index
      if (index !== -1 && data.item.factor == data.value) {
        this.answerArr[index].result = true;
      } else if (index == -1 && data.item.factor == data.value) {
        //If not already in answer array, and input is true, set obj
        let obj = {};
        obj.id = data.item.id;
        obj.result = true;
        this.answerArr.push(obj);
      } else if (!data) {
        //Empty input means it's false
        this.answerArr[index].result = false;
      }

      //Start getting counts for number of T or F
      let result = this.answerArr.map((a) => a.result).filter((a) => a == true);

      //Get number true from length of array of true's. Calculate false count from that and input
      this.trueCount = result.length;
      this.falseCount = this.inputCount - this.trueCount;
    },
    toggle() {
      if (!this.btnActive) {
        this.btnActive = true;
      } else {
        this.btnActive = false;
      }
    },
  },
  mounted() {
    this.init();
  },
};
</script>

<template>
  <main>
    <!--     <p>{{ "column factors: " + columnFactors.map((a) => a.factor) }}</p>
    <p>{{ "row factors: " + rowFactors.map((a) => a.factor) }}</p> -->

    <transition :name="correctFade" mode="out-in">
      <h1 v-if="!gameOver">Complete the Multiplication Grid</h1>
      <p v-else class="game-end">Great Job!</p>
    </transition>

    <div class="directions">
      <p>
        Light blue squares can contain only 1-9. <br />Dark blue squares are
        their products.
      </p>
    </div>
    <div class="board-container">
      <div class="multiplication-grid">
        <div class="column-factors" :style="gridStyleColumns">
          <!-- <div class="column-factors"> -->
          <div></div>
          <GridSquare
            class="value-cell"
            v-for="(item, index) in columnFactors"
            :item="item"
            :index="index"
            :key="item.id"
            :showing="[columnFactorVisibility, emptyBoxes]"
            @add-true="eventHandler"
          />
        </div>
        <div class="row-two-down">
          <div class="row-factors">
            <GridSquare
              class="value-cell"
              v-for="(item, index) in rowFactors"
              :item="item"
              :index="index"
              :key="item.id"
              :showing="[rowFactorVisibility, emptyBoxes]"
              @add-true="eventHandler"
            />
          </div>
          <div class="products" :style="gridStyleProducts">
            <GridSquare
              class="value-cell"
              v-for="(item, index) in products"
              :item="item"
              :index="index"
              :key="item.id"
              :showing="[productsVisibility, emptyBoxes]"
              @add-true="eventHandler"
            />
          </div>
        </div>
      </div>
      <!-- <p>{{ answerArr }}</p> -->
      <transition :name="correctFade" mode="out-in">
        <button
          v-if="!gameOver"
          v-on:click="showResult()"
          class="check-btn"
          :class="[btnActive ? 'check-btn' : 'other']"
          @click="toggle"
        >
          <strong>
            {{ btnActive ? "KEEP GOING" : "CHECK ANSWERS" }}
          </strong>
        </button>
        <button
          v-else
          class="play-again"
          @click="
            init();
            toggle();
          "
        >
          <strong> PLAY AGAIN! </strong>
        </button>
      </transition>
    </div>
    <!-- <p>
      total inputs: {{ totalInputs }} true:{{ trueCount }} false:{{ falseCount }}
    </p> -->
    <!-- <p>answerArr: {{ answerArr }}</p> -->
  </main>
</template>

<style>
body {
  background: #f5eeee;
}
h1,
p.game-end {
  text-align: center;
  margin-bottom: 0.5em;
}

div.directions {
  display: flex;
  justify-content: center;
}

div.directions > p {
  max-width: 31.5em;
  text-align: left;
  margin: 0em 0em 1em 0em;
}

@media (max-width: 500px) {
  h1,
  p.game-end {
    max-width: 13em;
    min-height: 3.2em;
  }
  p.game-end {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
}
@media (max-width: 550px) {
  h1,
  p.game-end {
    font-size: 1.8em;
    width: auto;
  }
}

div.board-container {
  max-width: fit-content;
  margin: 0 auto;
}
div.multiplication-grid {
  /* border: solid 0.2em magenta; */
  max-width: fit-content;
  display: grid;
  box-shadow: 10px 10px 5px lightgray;
}
div.column-factors {
  display: flex;
  flex-grow: 1;
  /* border: solid 0.4em chartreuse; */
  display: grid;
  /* grid-template-columns: repeat(9, 1fr); */
  background-color: #80b8f06e;
  border-left: solid 0.2em transparent;
}
div.row-two-down {
  display: flex;
}
div.row-factors {
  display: flex;
  flex-direction: column;
  border-top: solid 0.2em transparent;
  background-color: #80b8f06e;
  /* border: solid 0.2em red; */
}
div.column-factors > * {
  width: 5em;
  height: 5em;
  /* border: solid 0.1em black; */
  display: flex;
  justify-content: center;
  align-items: center;
  /* border-right: solid 0.2em transparent; */
  border-bottom: none;
}
div.products {
  display: flex;
  flex-wrap: wrap;
  /* border: solid 0.2em purple; */
  display: grid;
  border-top: solid 0.2em black;
  border-left: solid 0.2em black;
  background-color: #80b8f0;
}

button {
  margin-top: 1em;
  margin-left: auto;
  height: 4em;
  width: 12em;
  display: flex;
  justify-content: center;
  align-items: center;
}
button > strong {
  font-weight: 700;
}
button.check-btn {
  background: #34a034;
  border: solid 0.1em #34a034;
  color: white;
  border-radius: 0.2em;
}
button.other {
  background-color: #2563d4fa;
  border-color: #2563d4fa;
}
button.play-again {
  background: #f85555;
  border: solid 0.1em #f85555;
  color: white;
  border-radius: 0.2em;
}

.game-end {
  color: #f85555;
  text-align: center;
  font-size: 2em;
}

/*Flip Effect for Title -- changed to fade */
/* .flip-enter-active {
  animation-name: spin;
  -webkit-animation-name: spin;
  animation-timing-function: ease-in-out;
  -webkit-animation-timing-function: ease-in-out;
  animation-duration: 1s;
  -webkit-animation-duration: 1s;
  -webkit-transform-style: preserve-3d;
  -moz-transform-style: preserve-3d;
  -ms-transform-style: preserve-3d;
  transform-style: preserve-3d;
}

.flip-leave-active {
  animation: spin 1s reverse;
}
@keyframes spin {
  0% {
    transform: rotateY(90deg);
  }

  100% {
    transform: rotateY(0deg);
  }
} */

.fade-enter-active {
  animation: fade 1s linear forwards;
}

@keyframes fade {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

.fade-leave-active {
  animation: fade 1s reverse;
}
</style>
