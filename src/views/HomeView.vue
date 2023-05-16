<script setup lang="ts">
import { onBeforeMount, reactive, ref } from 'vue';

const data = reactive({
  query:[],
  article:[],
})
// to get content
// prop=revisions&rvprop=content


async function getData() {
/*   const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&list=random&rnnamespace=0&rnlimit=1&prop=revisions&rvprop=content&format=json&origin=*"); */
  const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&list=random&rnnamespace=0&rnlimit=10&format=json&origin=*");  
  const data = await response.json();
  console.log(data);
  return data;
}

async function getContent(title: string) {
/*   const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&titles=" + title + "&prop=revisions|pageviews&rvprop=content&rvslots=*&format=json&origin=*");  
 */  
  const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&titles=" + title + "&prop=revisions|pageviews&rvprop=content&rvslots=*&format=json&origin=*");  
  const article = await response.json();
  console.log(article);
  return article;
}

onBeforeMount(()=> {
  getData().then(response => {
    const title = response.query.random[0].title;
    const id = response.query.random[0].id;
    data.query = response;
    const content = getContent(title).then(content => {
      data.article = content.query.pages[id].revisions;
    });
  })
})


</script>

<template>
  <main>
    <div>{{ data.query.query.random[0].title }}</div>
    <div>{{ data.article[0].slots.main["*"] }}</div>
  </main>
</template>
