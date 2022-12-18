<script>
import GridSquare from "./components/GridSquare.vue";

export default {
  name: "App",
  components: { GridSquare },
  data() {
    return {
      factorValue: "",
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
      columnsFlag: "C",
      rowsFlag: "R",
      productsFlag: "P",
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
      this.inputCount = visArr.filter((value) => value === false).length;
      /* for (let i = 0; i < visArr.length; i++) {
        if (visArr[i] == false) {
          this.falseCount = this.falseCount + 1;
          console.log("false here");
        } */
      console.log(this.inputCount);
      return this.inputCount;
      //}
    },
  },
  methods: {
    randomOneToNine: function () {
      let randomFactor = Math.floor(Math.random() * 9) + 1;
      return (this.factorValue = randomFactor);
    },
    buildFactorArray: function () {
      let arr = [];
      console.log("arr " + arr);
      let randomDimensionTwoOrGreater = Math.floor(Math.random() * 7) + 2;
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
      return arr;
    },
    init: function () {
      this.columnFactors = this.buildFactorArray();
      this.rowFactors = this.buildFactorArray();
      this.products = this.buildProductArray();
      this.columnFactorVisibility = this.fiftyFiftyHideShow(
        this.columnFactors.length
      );
      this.rowFactorVisibility = this.fiftyFiftyHideShow(
        this.rowFactors.length
      );
      this.productsVisibility = this.productsHideShow(this.products.length);
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
          //console.log(this.rowFactors[0]);
          //console.log(this.columnFactors[0]);
          //console.log(colLength);
          let product = this.rowFactors[i] * this.columnFactors[j];
          console.log("product: " + testProd);
          arr.push(product);
        }
      }
      console.log(arr);
      return arr;
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
    /* setAnswers: function (data) {
      console.log("data:" + data.item);
      console.log("Value: " + data.value);
      console.log("Index: " + data.index);
      console.log(typeof data.value);
      if (data.value == data.item) {
        console.log(true);
        this.answerArr.push(true);
      } else {
        this.answerArr.push(false);
      }
    }, */
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

      if (this.inputCount == this.trueCount) {
        alert("done");
      }

      /* if (this.$refs.input.classList.contains("correct")) {
        console.log("yup");
      } */
      //this.isActive = !this.isActive;
      // some code to filter users
    },
    eventHandler(value, data) {
      //if index changes false to true, +1 to true Count
      //if index changes true to false, -1 true Count
      // for (let i = 0; i < data.index; i++) {
      if (data.item == data.value) {
        console.log("data item" + data.item);
        console.log("data value" + data.value);
        this.trueCount = this.trueCount + value;
        console.log("handle false");
      } else if (data.value && data.item !== data.value && this.trueCount > 0) {
        //this.trueCount = this.trueCount - value;
      }
      //this.trueCount = this.trueCount + value;
      this.falseCount = this.inputCount - this.trueCount;
      console.log("this true count" + this.trueCount);
    },
  },
  mounted() {
    this.init();
  },
};
</script>

<template>
  <!--   <header>
    <img
      alt="Vue logo"
      class="logo"
      src="./assets/logo.svg"
      width="125"
      height="125"
    />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
    </div>
  </header> -->

  <main>
    <!-- <TheGameGrid /> -->
    <p>{{ "column factors: " + columnFactors }}</p>
    <p>{{ "row factors: " + rowFactors }}</p>

    <div class="multiplication-grid">
      <div class="column-factors" :style="gridStyleColumns">
        <!-- <div class="column-factors"> -->
        <div>EMPTY</div>

        <GridSquare
          v-bind:valuesArray="[
            columnFactors,
            columnFactorVisibility,
            whiteBoxes,
            columnsFlag,
          ]"
          @input="setAnswers"
          @add-true="eventHandler"
        />
      </div>
      <div class="row-two-down">
        <div class="row-factors">
          <GridSquare
            v-bind:valuesArray="[
              rowFactors,
              rowFactorVisibility,
              whiteBoxes,
              rowsFlag,
            ]"
            @add-true="eventHandler"
          />
        </div>
        <div class="products" :style="gridStyleProducts">
          <GridSquare
            v-bind:valuesArray="[
              products,
              productsVisibility,
              whiteBoxes,
              productsFlag,
            ]"
            @add-true="eventHandler"
          />
        </div>
      </div>
    </div>
    <!-- <p>{{ answerArr }}</p> -->
    <button v-on:click="showResult()">CHECK ANSWERS</button>
    <p>
      Check {{ whiteBoxes }} total inputs: {{ totalInputs }} true:{{
        trueCount
      }}
      false:{{ falseCount }}
    </p>
  </main>
</template>

<style scoped>
div.multiplication-grid {
  border: solid 0.2em magenta;
  max-width: fit-content;
  display: grid;
}
div.column-factors {
  display: flex;
  flex-grow: 1;
  border: solid 0.4em chartreuse;
  display: grid;
  /* grid-template-columns: repeat(9, 1fr); */
}
div.row-two-down {
  display: flex;
}
div.row-factors {
  display: flex;
  flex-direction: column;
  border: solid 0.2em red;
}
div.column-factors > * {
  min-width: 6em;
  display: flex;
  justify-content: center;
  align-items: center;
}
div.products {
  display: flex;
  flex-wrap: wrap;
  border: solid 0.2em purple;
  display: grid;
  /* grid-template-columns: repeat(9, 1fr); */
}

/* header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
} */
</style>
