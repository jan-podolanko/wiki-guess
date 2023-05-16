<script setup lang="ts">
import { onBeforeMount, ref } from 'vue';
import TheWelcome from '../components/TheWelcome.vue'

let query: any;


async function getData() {
  const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&list=random&rnnamespace=0&rnlimit=5&format=json&origin=*");
  const data = await response.json();
  console.log(data)
  return data;
}

onBeforeMount(()=> {
  getData().then(data => {
    query = data;
  })
})


</script>

<template>
  <main>
    <div>{{ query.query }}</div>
  </main>
</template>
