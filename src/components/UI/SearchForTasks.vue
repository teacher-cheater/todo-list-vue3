<template>
  <input @input="updateInput" :value="modelValue" class="input" type="text" />
</template>

<script>
export default {
  name: "search-for-tasks",
  props: {
    modelValue: [String, Number],
  },
  methods: {
    updateInput(event) {
      this.$emit("update:modelValue", event.target.value);
    },
  },
};
</script>

<style scoped></style>
