
<template>
  <input ref="inputRef" :value="formattedValue" />
</template>

<script>
import { useNumberInput } from 'vue-intl-number-input';

export default {
  name: "NumberInput",
  props: {
    modelValue: Number,
    options: Object,
  },
  watch: {
    modelValue: function (num)    {
      console.log("nimimp: " + num)
    }
  },
  setup(props) {
    const { formattedValue, inputRef } = useNumberInput(props.options);

    return { inputRef, formattedValue };
  },
};
</script>
