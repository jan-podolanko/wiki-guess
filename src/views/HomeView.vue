<script setup lang="ts">
import { onBeforeMount, reactive, ref } from 'vue';
import { convert } from 'html-to-text';

const data = reactive({
  query:[],
  pageviews: Number,
  title: String,
  pageid: String,
  content: String,
  plaintext: String,
})
// to get content
// prop=revisions&rvprop=content
const url = "https://en.wikipedia.org/w/api.php"

async function getData() {
/*   const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&list=random&rnnamespace=0&rnlimit=1&prop=revisions&rvprop=content&format=json&origin=*"); */
  const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&generator=random&grnnamespace=0&grnlimit=1&prop=info&format=json&origin=*");  
  const data = await response.json();
  console.log(data);
  return data;
}

async function getContent(title: string) {
/*   const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&titles=" + title + "&prop=revisions|pageviews&rvprop=content&rvslots=*&format=json&origin=*");  
 */  
  const response = await fetch("https://en.wikipedia.org/w/api.php?action=parse&page=" + title + "&prop=text&formatversion=2&format=json&origin=*");  
  const article = await response.json();
  console.log(article);
  return article;
}

onBeforeMount(()=> {
  getData().then(response => {
    const query = response.query.pages;
    data.query = response.query;
    const title = Object.values(query)[0].title;
    const content = getContent(title).then(article => {
      data.title = article.parse.title
      data.pageid = article.parse.pageid
      data.content = article.parse.text;
      data.plaintext = convert(article.parse.text);
    });
  })
})


</script>

<template>
  <main>
    <div>{{ data.query }}</div>
    <div>{{ data.title }}</div>
    <div>{{ data.content }}</div>
    <div>{{ data.plaintext }}</div>
<!--     <div v-html="data.article.parse.text"></div>
 -->   </main>
</template>
