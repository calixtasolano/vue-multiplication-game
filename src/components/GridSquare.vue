<script>
export default {
  name: "GridSquare",
  data() {
    return {
      // lineToDelete: "",
      //enteredValue: "",
      //colFactorVisibility: [],
      //products: this.buildProductArray(),
    };
  },
  props: {
    valuesArray: {
      type: Object,
    },
    id: {
      type: Number,
    },
    //emits: ["update:enteredValue"],
    /* factor: {
      type: Number,
    }, */
  },
  methods: {
    hideShow: function () {
      if (Math.random() < 0.5) return true;
    },
  },
};
</script>

<template>
  <div class="value-cell" v-for="(value, index) in valuesArray">
    <p class="given-value" v-if="hideShow()">{{ value }}</p>
    <input
      type="number"
      placeholder="Num"
      v-else
      @input="$emit('input', { value: $event.target.value, id, value })"
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
}

.correct {
  background-color: green;
}

.incorrect {
  background-color: red;
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
