<template>
  <div class="arrows">
    &nbsp;
    <div class="quantity-nav">
      <svg
        @click="increment"
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M5 15l7-7 7 7"
        />
      </svg>
      <svg
        xmlns="http://www.w3.org/2000/svg"
        @click="decrement"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M19 9l-7 7-7-7"
        />
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: "InputNumberArrows",
  props: { modelValue: Number,
  disabled: Boolean
  },

  emits: ["update:modelValue"],
  data() {
    return {
      isInputActive: false,
    };
  },
  methods: {
    increment() {
      if (!this.disabled) this.$emit("update:modelValue", this.modelValue + 1);
    },
    decrement() {
      if (!this.disabled && this.modelValue > 0)this.$emit("update:modelValue", this.modelValue - 1);
    },
  },
};
</script>

<style scoped>
.arrows {
  position: relative;
}

.quantity-nav {
  display: flex;
  cursor: pointer;
  position: absolute;
  float: left;
  top: 0.2rem;
  right: 0.3rem;
  flex-direction: column;
}

.quantity-nav svg {
  color: gray;
  height: 10px;
  width: 10px;
  user-select: none;
}
</style>
