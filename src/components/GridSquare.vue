<script>
export default {
  name: "GridSquare",
  data() {
    return {
      answerArr2: {},
      trueCount: 0,
      id: 0,
    };
  },
  props: {
    valuesArray: {
      type: Object,
    },
    emits: ["addTrue"],
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
    },
    checkActive: function (data) {
      console.log("data:" + data.item.factor);
      console.log("Value: " + data.value);
      console.log("Index: " + data.index);
      console.log("Id: " + data.item.id);

      console.log(typeof data.value);
      this.$emit("addTrue", data);
      if (data.value == data.item.factor) {
        this.answerArr2[data.index] = true;
      } else {
        this.answerArr2[data.index] = false;
      }
    },
  },
};
</script>

<template>
  <div class="value-cell" v-for="(item, index) in valuesArray[0]">
    <p class="given-value" v-if="valuesArray[1][index]">{{ item.factor }}</p>
    <input
      type="number"
      placeholder="Number"
      v-else
      ref="input"
      @blur="checkActive({ value: $event.target.value, item, index })"
      :class="[
        this.answerArr2[index] ? 'correct' : 'incorrect',
        { notShowing: valuesArray[2] },
      ]"
      :disabled="!valuesArray[2]"
      min="1"
      max="100"
    />
  </div>
</template>

<style scoped>
div.value-cell {
  width: 6em;
  height: 6em;
  border: solid 0.1em black;
  display: flex;
  justify-content: center;
  align-items: center;
  border-right: none;
  border-bottom: none;
}

div.value-cell > input {
  height: 4em;
  border-color: darkgray;
  border-style: solid;
  text-align: center;
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
/* 
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

} */
</style>
