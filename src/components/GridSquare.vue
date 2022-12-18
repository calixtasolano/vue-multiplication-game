<script>
export default {
  name: "GridSquare",
  data() {
    return {
      //isActive: false,
      answerArr2: {},
      trueCount: 0,
      id: 0,
      //falseCount: 0,
    };
  },
  props: {
    valuesArray: {
      type: Object,
    },
    /* id: {
      type: Number,
    }, */

    /* falseCount: {
      type: Number,
    }, */
    //showIt: { type: Boolean },
    //emits: ["input"],
  },
  computed: {},
  methods: {
    hideShow: function (data) {
      if (Math.random() < 0.5) return true;
    },
    idIncrement(idx, showing) {
      console.log("compute id");
      if (!showing) {
        this.id = this.id + 1;
      }
      //this.id = this.id + 1;
      //let colNum = this.columnFactors.length + 1;
      //return this.answerArr2;
    },
    checkActive: function (data) {
      console.log("data:" + data.item);
      console.log("Value: " + data.value);
      console.log("Index: " + data.index);
      console.log("Id: " + data.id);
      console.log("Flag: " + data.flag);
      console.log(typeof data.flag);
      console.log(typeof data.value);
      if (data.value == data.item) {
        console.log(true);
        //const element = this.$refs.input;
        //console.log(element[data.index]);
        //this.isActive = true;
        //let tempObj;
        //tempObj = { id: data.item };
        //this.answerArr2[this.id] = data.item;
        //let tempObJ;
        this.answerArr2[data.index] = true;
        //this.answerArr2[data.flag][data.index] = true;
        //console.log("tsting flag " + this.answerArr2[data.flag]);
        //this.answerArr2[data.index] = true;
        this.trueCount++;
        //if (this.answerArr2[data.index] == true) {

        //}
        console.log(this.trueCount);
        //this.answerArr.push(true);
      } else {
        //this.isActive = false;
        this.answerArr2[data.index] = false;
        this.falseCount++;
        console.log(this.falseCount);
        //this.answerArr.push(false);
      }
      this.$emit("add-true", 1, data);
      console.log(this.answerArr2);
    },
    countTrueFalse: function () {
      for (i = 0; i < this.answerArr2.length; i++) {
        if (this.answerArr2[i] == true) {
          this.trueCount++;
        }
        //combine all three visibility arrays (true means p showing and input hidden), loop through them and get all false to get input count
        //count all true answers and subtract from number of inputs to get false count.
      }
    },
  },
};
</script>

<template>
  <div class="value-cell" v-for="(item, index) in valuesArray[0]">
    <p class="given-value" v-if="valuesArray[1][index]">{{ item }}</p>
    <input
      type="number"
      placeholder="Num"
      v-else
      ref="input"
      @blur="checkActive({ value: $event.target.value, id, item, index })"
      :class="[
        this.answerArr2[index] ? 'correct' : 'incorrect',
        { notShowing: valuesArray[2] },
      ]"
      :disabled="!valuesArray[2]"
      min="1"
      max="100"
    />
  </div>
  <!-- <div class="item">
    <i>
      <slot name="icon"></slot>
    </i>
    <div class="details">
      <h3>
        <slot name="heading"></slot>
      </h3>
      <slot></slot>
    </div>
  </div> -->
</template>

<style scoped>
div.value-cell {
  width: 6em;
  height: 6em;
  border: solid 0.2em black;
  display: flex;
  justify-content: center;
  align-items: center;
}

div.value-cell > input {
  height: 3em;
  border-color: darkgray;
  border-style: solid;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: lightcoral;
}

.notShowing {
  background-color: white;
}
/* .item {
  margin-top: 2rem;
  display: flex;
}

.details {
  flex: 1;
  margin-left: 1rem;
}

i {
  display: flex;
  place-items: center;
  place-content: center;
  width: 32px;
  height: 32px;

  color: var(--color-text);
}

h3 {
  font-size: 1.2rem;
  font-weight: 500;
  margin-bottom: 0.4rem;
  color: var(--color-heading);
}

@media (min-width: 1024px) {
  .item {
    margin-top: 0;
    padding: 0.4rem 0 1rem calc(var(--section-gap) / 2);
  }

  i {
    top: calc(50% - 25px);
    left: -26px;
    position: absolute;
    border: 1px solid var(--color-border);
    background: var(--color-background);
    border-radius: 8px;
    width: 50px;
    height: 50px;
  }

  .item:before {
    content: " ";
    border-left: 1px solid var(--color-border);
    position: absolute;
    left: 0;
    bottom: calc(50% + 25px);
    height: calc(50% - 25px);
  }

  .item:after {
    content: " ";
    border-left: 1px solid var(--color-border);
    position: absolute;
    left: 0;
    top: calc(50% + 25px);
    height: calc(50% - 25px);
  }

  .item:first-of-type:before {
    display: none;
  }

  .item:last-of-type:after {
    display: none;
  }
} */
</style>
