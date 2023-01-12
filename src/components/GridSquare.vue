<script>
export default {
  name: "GridSquare",
  data() {
    return {
      flagTF: [],
    };
  },
  props: {
    showing: {
      type: Object,
    },
    item: {
      type: Object,
    },
    index: Number,
  },
  emits: ["addTrue"],
  computed: {},
  methods: {
    checkActive: function (data) {
      this.$emit("addTrue", data);
      if (data.value == data.item.factor) {
        this.flagTF[data.index] = true;
      } else {
        this.flagTF[data.index] = false;
      }
    },
  },
};
</script>

<template>
  <div>
    <p class="given-value" v-if="showing[0][index]">{{ item.factor }}</p>
    <input
      type="number"
      placeholder="Number"
      v-else
      @blur="checkActive({ value: $event.target.value, item, index })"
      :class="[
        this.flagTF[index] ? 'correct' : 'incorrect',
        { notShowing: showing[1] },
      ]"
      :disabled="!showing[1]"
      min="1"
      max="100"
    />
  </div>
</template>

<style scoped>
.value-cell {
  width: 6em;
  height: 6em;
  border: solid 0.1em black;
  display: flex;
  justify-content: center;
  align-items: center;
  border-right: none;
  border-bottom: none;
}

.value-cell > input {
  height: 4em;
  border-color: darkgray;
  border-style: solid;
  text-align: center;
  color: #2c3e50;
}

.value-cell > input::placeholder {
  color: #2c3e50;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: lightcoral;
}

.notShowing {
  background-color: #f0f080;
}
</style>
