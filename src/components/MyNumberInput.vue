<template>
  <input type="text" v-model="displayValue" @blur="isInputActive = false"
  @focus="isInputActive = true" @input="change"/>
</template>
<script>
  export default{
    name: "MyNumberInput",
    props: { modelValue : Number },
    emits: ['update:modelValue'],
    data () {
        return {
            isInputActive: false
        }
    },

    computed: {
        displayValue: {
            get: function() {
                if (this.isInputActive) {
                    // Cursor is inside the input field. unformat display value for user
                    return this.modelValue.toString()
                } else {
                    // User is not modifying now. Format display value for user interface
                  return this.modelValue.toString().replace(/\B(?=(\d{3})+(?!\d))/g, " ");
                }
            },
            set: function(modifiedValue) {
                // Recalculate value after ignoring "$" and "," in user input
                let newValue = parseFloat(modifiedValue.replace(/[^\d] /g, ""))
                // Ensure that it is not NaN
                if (isNaN(newValue)) {
                    newValue = 0
                }
                // Note: we cannot set this.value as it is a "prop". It needs to be passed to parent component
                // $emit the event so that parent component gets it
                this.$emit('update:modelValue', newValue)
            }
        }
    }
  }
</script>

<style scoped>

</style>
