<script setup>
defineProps({
  times: {
    type: Array,
    default: () => []
  }
});
</script>

<template>
  <div v-if="times.length">
    <p><strong>Занятые времена:</strong></p>
    <ul>
      <li v-for="(time, index) in times" :key="index">{{ time }}</li>
    </ul>
  </div>
  <div v-else>
    <p>Все времена свободны</p>
  </div>
</template>

<style scoped>
ul {
  padding-left: 1em;
}
li {
  list-style-type: disc;
}
</style>
