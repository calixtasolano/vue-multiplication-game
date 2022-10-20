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
      //tempObj: {},
      answerArr: [],
      products: [],
    };
  },
  computed: {},
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
    setAnswers: function (data) {
      console.log("Value: " + data.value);
      console.log(typeof data.value);
      console.log("Factor: " + data.factor);
      if (data.value == data.factor) {
        console.log(true);
        this.answerArr.push(true);
      } else {
        this.answerArr.push(false);
      }
      //console.log("DATA: " + data.value == data.factor);
      //this.answerArr[data.id] = data.value;
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
      <div class="column-factors">
        <div>EMPTY</div>
        <GridSquare v-bind:valuesArray="columnFactors" @input="setAnswers" />
      </div>
      <div class="row-two-down">
        <div class="row-factors">
          <GridSquare v-bind:valuesArray="rowFactors" />
        </div>
        <div class="products">
          <GridSquare v-bind:valuesArray="products" />
        </div>
      </div>
    </div>
    <p>{{ answerArr }}</p>
  </main>
</template>

<style scoped>
div.multiplication-grid {
  /* display: flex;
  flex-wrap: wrap; */
  border: solid 0.2em magenta;
  max-width: fit-content;
}
div.column-factors {
  display: flex;
  flex-grow: 1;
  border: solid 0.4em chartreuse;
}

div.row-two-down {
  display: flex;
}

div.row-factors {
  /* flex-direction: column;
  display: flex; */
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
  /* display: flex;
  flex-direction: row; */
  display: flex;
  flex-wrap: wrap;
  border: solid 0.2em purple;
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
