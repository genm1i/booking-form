<script setup>
defineProps({
  times: {
    type: Array,
    default: () => []
  }
});
</script>

<template>
  <div style="margin-top: 1em;">
    <div v-if="times.length">
      <p><strong>Занятые времена:</strong></p>
      <ul>
        <li v-for="(time, index) in times" :key="time">{{ time }}</li>
      </ul>
    </div>
    <div v-else>
      <p>Все времена свободны</p>
    </div>
  </div>
</template>

<style scoped>
ul {
  padding-left: 1.2em;
}
li {
  list-style-type: disc;
}
</style>
