<script>
import { buildDirectiveArgs } from "@vue/compiler-core";
import GridSquare from "./components/GridSquare.vue";

//Handle mobile view, which requires a smaller game grid
let colDim, rowDim;
const mediaQuery = window.matchMedia("(min-width: 768px)");

function handleTabletChange(e) {
  // Check if the media query is true
  if (e.matches) {
    // Then log the following message to the console
    console.log("Media Query Matched!");
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
    };
  },
  computed: {
    //dymnamic grid layout for column headers
    gridStyleColumns() {
      let colNum = this.columnFactors.length + 1;
      return {
        "grid-template-columns": "repeat(" + colNum + ", 1fr)",
        // order: 1
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
      console.log("visArr " + visArr);
      console.log("visArr length" + visArr.length);
      let inputTotal = visArr.filter((value) => value === false).length;
      console.log(inputTotal);
      //Get total of inputs on grid
      return inputTotal;
    },
  },
  methods: {
    buildFactorArray: function (val) {
      //Build column and row factors
      console.log(val);
      let arr = [];
      console.log("arr " + arr);
      //Get random length for array (random integer between 2 and colDim or rowDim) -- define dimensions of grid
      let randomDimensionTwoOrGreater = this.getRandomIntInclusive(2, val);
      console.log("rl " + randomDimensionTwoOrGreater);
      do {
        //Generate random int for new factor
        let newFactor = this.getRandomIntInclusive(1, 9);
        console.log("nf: " + newFactor);
        //Ensure that the factor isn't duplicated
        if (arr.indexOf(newFactor) === -1) {
          arr.push(newFactor);
          console.log("already here?");
        }
      } while (arr.length < randomDimensionTwoOrGreater);
      console.log(arr);

      //Generate id for each factor
      arr = this.addIds(arr);
      console.log("calling new function");
      console.log(arr);
      return arr;
    },
    //Increment to generate id for each box (factor)
    buildId: function () {
      this.id = this.id + 1;
      return this.id;
    },
    addIds: function (array) {
      console.log("in new function");
      console.log(array);
      let idValuesArray = [];
      for (let i = 0; i < array.length; i++) {
        let obj = {};
        obj.factor = array[i];
        obj.id = this.buildId();
        console.log(obj);
        idValuesArray.push(obj);
      }
      console.log(idValuesArray);
      //Array of factors with associated ids
      return idValuesArray;
    },
    //Initialize game grid
    init: function () {
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
      //console.log("cf: " + this.columnFactors);
      //console.log("cf length: " + this.columnFactors.length);

      //console.log("rf: " + this.rowFactors);
      //console.log("rf length: " + this.rowFactors.length);

      //let colLength = this.columnFactors.length;

      //let testProd = this.rowFactors[0].factor * this.columnFactors[0].factor;
      //console.log("testProd: " + testProd);

      //For each row, calculate the product down the row (across the column headers)
      for (let i = 0; i <= this.rowFactors.length - 1; i++) {
        for (let j = 0; j <= this.columnFactors.length - 1; j++) {
          let product =
            this.rowFactors[i].factor * this.columnFactors[j].factor;
          //console.log("product: " + testProd);
          arr.push(product);
        }
      }

      //Generate id for each factor
      arr = this.addIds(arr);
      console.log("calling new function");
      console.log(arr);
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
        }
      }
      console.log(arr);
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

      //TO DO: do I need these??
      let productLength = this.products.length;
      console.log("PL " + productLength);
      let columnLength = this.columnFactors.length;
      console.log("CL " + columnLength);
      let rowLength = this.rowFactors.length;
      console.log("CL " + rowLength);

      let randomSetToShowInProducts = [];

      //Run the random hide/show for the length of the products array
      randomSetToShowInProducts = this.fiftyFiftyHideShow(arrLength);
      console.log(randomSetToShowInProducts);

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
        console.log(rowSet);
        //If row doesn't include one true, pick a random entry from the row and set it to true.
        if (!rowSet.includes(true)) {
          console.log("had to set it");
          //Get random index using getRandomIntInclusive(min, max);
          randomFromRow = this.getRandomIntInclusive(
            startIndex,
            startIndex + columnLength - 1
          );
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
        console.log(indexesOfTrue);
        //Get array of mods from indices to check all are covered
        arrayOfMods = indexesOfTrue.map((x) => x % columnLength);
        console.log(arrayOfMods);
      }

      for (let i = 0; i < columnLength; i++) {
        //If array of mods doesn't cover all columns, pick a random row number, add mod and set that spot to be true.
        if (!arrayOfMods.includes(i)) {
          console.log("had to make one");
          let randomFromColumn =
            columnLength * this.getRandomIntInclusive(0, rowLength - 1) + i;
          randomSetToShowInProducts[randomFromColumn] = true;
        }
      }
      console.log("products visibility array " + randomSetToShowInProducts);
      //Return ammended visibility array for products -- ensures at least 1 number showing per row and column
      return randomSetToShowInProducts;
    },
    //Check button toggles the empty box color and checks if done with game
    showResult: function (event) {
      console.log("button");
      console.log(this.emptyBoxes);
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
        console.log(obj);
        this.answerArr.push(obj);
      } else {
        //Empty input means it's false
        this.answerArr[index].result = false;
      }

      console.log(this.answerArr);

      //Start getting counts for number of T or F
      let result = this.answerArr.map((a) => a.result).filter((a) => a == true);
      console.log(result);

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

    <transition :name="correctFlip" mode="out-in">
      <h1 v-if="!gameOver">Complete the Multiplication Grid</h1>
      <p v-else class="game-end">Great Job!</p>
    </transition>

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
        {{ btnActive ? "KEEP GOING" : "CHECK ANSWERS" }}
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
h1 {
  text-align: center;
}
@media (max-width: 550px) {
  h1,
  p.game-end {
    min-height: 3.5em;
  }
}
div.multiplication-grid {
  /* border: solid 0.2em magenta; */
  max-width: fit-content;
  display: grid;
  margin: 0 auto;
}
div.column-factors {
  display: flex;
  flex-grow: 1;
  /* border: solid 0.4em chartreuse; */
  display: grid;
  /* grid-template-columns: repeat(9, 1fr); */
  background-color: #80b8f08a;
  border-left: solid 0.2em transparent;
}
div.row-two-down {
  display: flex;
}
div.row-factors {
  display: flex;
  flex-direction: column;
  border-top: solid 0.2em transparent;
  background-color: #80b8f08a;
  /* border: solid 0.2em red; */
}
div.column-factors > * {
  width: 6em;
  height: 6em;
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
button.check-btn {
  background: green;
  border: solid 0.1em red;
  color: white;
  border-radius: 0.2em;
  border-color: green;
}
button.other {
  background-color: hsl(219deg 73% 46% / 98%);
  border-color: hsl(219deg 73% 46% / 98%);
}
button.play-again {
  background: #f85555;
  border: solid 0.1em #f85555;
  color: white;
  border-radius: 0.2em;
}

/*

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
} */

.game-end {
  color: #f85555;
  text-align: center;
  font-size: 2em;
}

.flip-enter-active {
  animation-name: spin;
  -webkit-animation-name: spin;

  animation-timing-function: ease-in-out;
  -webkit-animation-timing-function: ease-in-out;

  animation-duration: 2s;
  -webkit-animation-duration: 2s;

  -webkit-transform-style: preserve-3d;
  -moz-transform-style: preserve-3d;
  -ms-transform-style: preserve-3d;
  transform-style: preserve-3d;
}

.flip-leave-active {
  animation: spin 2s reverse;
}
@keyframes spin {
  0% {
    transform: rotateY(90deg);
  }

  100% {
    transform: rotateY(0deg);
  }
}

.fade-enter-active {
  animation: fade 2s linear forwards;
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
  animation: fade 2s reverse;
}
</style>
