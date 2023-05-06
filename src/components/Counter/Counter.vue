<script setup>
import { toRefs, ref, reactive } from "vue";

const props = defineProps({
  count: Number,
});
</script>

<template>
  <div class="">
    <p>you have {{ props.count }} task{{ props.count > 1 ? "s" : "" }}</p>
  </div>
</template>
