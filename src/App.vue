<script>
import { buildDirectiveArgs } from "@vue/compiler-core";
import GridSquare from "./components/GridSquare.vue";

let max;

/* function myFunction(x) {
  if (x.matches) {
    // If media query matches
    max = 2;
    //document.body.style.backgroundColor = "yellow";
  } else {
    //document.body.style.backgroundColor = "pink";
    max = 8;
  }
  return max;
}

var x = window.matchMedia("(max-width: 700px)");
myFunction(x); // Call listener function at run time
x.addListener(myFunction); */

const mediaQuery = window.matchMedia("(min-width: 768px)");

function handleTabletChange(e) {
  // Check if the media query is true
  if (e.matches) {
    // Then log the following message to the console
    console.log("Media Query Matched!");
    max = 8;
  } else {
    max = 2;
  }
}

// Register event listener
//mediaQuery.addListener(handleTabletChange);

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
      factorValue: "",
      id: 0,
      columnFactors: [],
      rowFactors: [],
      columnFactorVisibility: [],
      rowFactorVisibility: [],
      productsVisibility: [],
      answerArr: [],
      products: [],
      whiteBoxes: true,
      inputCount: 0,
      trueCount: 0,
      falseCount: 0,
      btnActive: false,
    };
  },
  computed: {
    gridStyleColumns() {
      console.log("hello");
      let colNum = this.columnFactors.length + 1;
      return {
        "grid-template-columns": "repeat(" + colNum + ", 1fr)",
        // order: 1
      };
    },
    gridStyleProducts() {
      console.log("hello");
      let colNum = this.columnFactors.length;
      return {
        "grid-template-columns": "repeat(" + colNum + ", 1fr)",
        // order: 1
      };
    },
    totalInputs() {
      let visArr;
      visArr = this.columnFactorVisibility.concat(
        this.rowFactorVisibility,
        this.productsVisibility
      );
      console.log("visArr " + visArr);
      console.log("visArr length" + visArr.length);
      let inputTotal = visArr.filter((value) => value === false).length;
      console.log(inputTotal);
      return inputTotal;
      //}
    },
  },
  methods: {
    randomOneToNine: function () {
      let randomFactor = Math.floor(Math.random() * 9) + 1;
      return (this.factorValue = randomFactor);
    },
    buildFactorArray: function (val) {
      console.log(val);
      let arr = [];
      console.log("arr " + arr);
      let randomDimensionTwoOrGreater = Math.floor(Math.random() * val) + 2;
      console.log("rl " + randomDimensionTwoOrGreater);

      do {
        let newFactor = this.randomOneToNine();
        console.log("nf: " + newFactor);

        if (arr.indexOf(newFactor) === -1) {
          arr.push(newFactor);
          console.log("already here?");
        }
      } while (arr.length < randomDimensionTwoOrGreater);
      console.log(arr);

      let idValuesArray = [];
      for (let i = 0; i < arr.length; i++) {
        let obj = {};
        obj.factor = arr[i];
        obj.id = this.buildId();
        console.log(obj);
        idValuesArray.push(obj);
      }

      console.log(idValuesArray);
      return idValuesArray;
    },
    buildId: function () {
      this.id = this.id + 1;
      return this.id;
    },
    init: function () {
      this.columnFactors = this.buildFactorArray(max);
      this.rowFactors = this.buildFactorArray(max);
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
    },
    buildProductArray: function () {
      let arr = [];
      console.log("cf: " + this.columnFactors);
      console.log("cf length: " + this.columnFactors.length);

      console.log("rf: " + this.rowFactors);
      console.log("rf length: " + this.rowFactors.length);

      let colLength = this.columnFactors.length;

      let testProd = this.rowFactors[0] * this.columnFactors[0];
      console.log("testProd: " + testProd);

      for (let i = 0; i <= this.rowFactors.length - 1; i++) {
        for (let j = 0; j <= this.columnFactors.length - 1; j++) {
          let product =
            this.rowFactors[i].factor * this.columnFactors[j].factor;
          console.log("product: " + testProd);
          arr.push(product);
        }
      }
      let idValuesArray = [];
      for (let i = 0; i < arr.length; i++) {
        let obj = {};
        obj.factor = arr[i];
        obj.id = this.buildId();
        console.log(obj);
        idValuesArray.push(obj);
      }

      console.log(idValuesArray);
      return idValuesArray;
    },
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
      //Make sure there is 1 in each row...
      // take products indices and make array of those
      // splice them into x arrays each of length col.length
      //pick one random element from each array
      //push to temp array
      //loop through all products in array hide/show on 50/50
      //set hide/show[index from temp array] all to show (true)

      //move to computed??
      let productLength = this.products.length;
      console.log("PL " + productLength);
      let columnLength = this.columnFactors.length;
      console.log("CL " + columnLength);

      let randomSetToShowInProducts = [];

      randomSetToShowInProducts = this.fiftyFiftyHideShow(arrLength);
      let randomIndexInEachRowArr = [];

      //be sure one is showing in each row
      for (
        let startIndex = 0;
        startIndex < productLength;
        startIndex = startIndex + columnLength
      ) {
        //get random index from each row using getRandomIntInclusive(min, max);
        let randomFromRow = this.getRandomIntInclusive(
          startIndex,
          startIndex + columnLength - 1
        );
        //console.log(randomFromRow);
        randomIndexInEachRowArr.push(randomFromRow);
        console.log("random index: " + randomIndexInEachRowArr);
        randomSetToShowInProducts[randomFromRow] = true;
      }
      console.log("products visibility array " + randomSetToShowInProducts);

      return randomSetToShowInProducts;
    },
    showResult: function (event) {
      console.log("button");
      console.log(this.whiteBoxes);
      this.whiteBoxes = !this.whiteBoxes;

      if (this.inputCount === this.trueCount) {
        alert("done");
      }
    },
    eventHandler(data) {
      //if index changes false to true, +1 to true Count
      //if index changes true to false, -1 true Count

      //if id exists

      const index = this.answerArr.findIndex((object) => {
        return object.id == data.item.id;
      });

      if (index !== -1 && data.item.factor == data.value) {
        this.answerArr[index].result = true;
      } else if (index == -1 && data.item.factor == data.value) {
        let obj = {};
        obj.id = data.item.id;
        obj.result = true;
        console.log(obj);
        this.answerArr.push(obj);
      } else {
        this.answerArr[index].result = false;
      }

      console.log(this.answerArr);
      let result = this.answerArr.map((a) => a.result).filter((a) => a == true);
      console.log(result);

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
    <!-- <div>
      Correct!
      <button
        @click="
          init();
          toggle();
        "
      >
        Play again
      </button>
    </div> -->
    <h1>Complete the Multiplication Grid</h1>
    <h2>(Fill in the missing spots)</h2>
    <div class="multiplication-grid">
      <div class="column-factors" :style="gridStyleColumns">
        <!-- <div class="column-factors"> -->
        <div>EMPTY</div>
        <GridSquare
          v-bind:valuesArray="[
            columnFactors,
            columnFactorVisibility,
            whiteBoxes,
          ]"
          @add-true="eventHandler"
        />
      </div>
      <div class="row-two-down">
        <div class="row-factors">
          <GridSquare
            v-bind:valuesArray="[rowFactors, rowFactorVisibility, whiteBoxes]"
            @add-true="eventHandler"
          />
        </div>
        <div class="products" :style="gridStyleProducts">
          <GridSquare
            v-bind:valuesArray="[products, productsVisibility, whiteBoxes]"
            @add-true="eventHandler"
          />
        </div>
      </div>
    </div>
    <!-- <p>{{ answerArr }}</p> -->
    <button
      v-on:click="showResult()"
      class="check-btn"
      :class="[btnActive ? 'check-btn' : 'other']"
      @click="toggle"
    >
      {{ btnActive ? "KEEP GOING" : "CHECK ANSWERS" }}
    </button>
    <!-- <p>
      Check {{ whiteBoxes }} total inputs: {{ totalInputs }} true:{{
        trueCount
      }}
      false:{{ falseCount }}
    </p> -->
    <!-- <p>answerArr: {{ answerArr }}</p> -->
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
h2 {
  margin-bottom: 1em;
  text-align: center;
}
div.multiplication-grid {
  /* border: solid 0.2em magenta; */
  max-width: fit-content;
  display: grid;
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

button.check-btn {
  margin-top: 2em;
  margin-left: auto;
  height: 4em;
  width: 12em;
  background: green;
  border: solid 0.1em red;
  color: white;
  border-radius: 0.2em;
  border-color: green;
  display: flex;
  justify-content: center;
  align-items: center;
}
button.other {
  background-color: hsl(219deg 73% 46% / 98%);
  border-color: hsl(219deg 73% 46% / 98%);
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

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: lightcoral;
}

.notShowing {
  background-color: white;
}
</style>
