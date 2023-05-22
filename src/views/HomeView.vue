<script setup lang="ts">
import { onBeforeMount, reactive, ref } from 'vue';

const data = reactive({
  query:[],
  pageviews: Number,
  title: String,
  pageid: String,
  content: [],
  plaintext: String,
  sections:[],
})
// to get content
// prop=revisions&rvprop=content
const url = "https://en.wikipedia.org/w/api.php"

async function getData() {
  const response = await fetch("https://en.wikipedia.org/w/api.php?action=query&generator=random&grnnamespace=0&grnlimit=1&prop=info&disabletoc=1&format=json&origin=*");  
  const data = await response.json();
  return data;
}

async function getContent(id: any) {
  const sections_resp = await fetch("https://en.wikipedia.org/w/api.php?action=parse&pageid=" + id + "&prop=sections&formatversion=2&disabletoc=1&format=json&origin=*");  
  const sections_json = await sections_resp.json();
  const sect_arr = sections_json.parse.sections;
  data.sections = sect_arr;
  const article_arr: any = [];

  //add sections that arent references or links to the array
  for(let i = 0; i < sect_arr.length; i++){
    console.log(sect_arr[i].anchor);
    if(sect_arr[i].anchor !== "References" || "External_links"){
      let response = await fetch("https://en.wikipedia.org/w/api.php?action=parse&pageid=" + id + "&prop=text" + "&section=" + i + "&formatversion=2&disabletoc=1&format=json&origin=*");  
      let sect_text = await response.json();
      article_arr.push(sect_text);
    }
  }
  return article_arr;
}

onBeforeMount(()=> {
  getData().then(response => {
    const query = response.query.pages;
    data.query = response.query;
    data.pageid = Object.values(query)[0].pageid;
    const title = Object.values(query)[0].title;
    const content = getContent(data.pageid).then(article => {
      /* data.title = article.parse.title
      data.pageid = article.parse.pageid
      data.content = article.parse.text; */
      console.log(article);
      data.content = Array(article);
    });
  })
})


</script>

<template>
  <main>
    <div>{{ data.query }}</div>
    <div>{{ data.sections }}</div>
    <div v-for="(section, index) in data.content[0]">
      <div>SECTON START</div>
      <br>
      <div v-html="section.parse.text"></div>
      <br>
      <div>SECTON END</div>
    </div>
  </main>
</template>
