<script setup lang="ts">
import { ref, } from "vue";
import MarkdownEditor from "./components/MarkdownEditor.vue"
import HTMLRender from "./components/HTMLRender.vue";

const mdText = ref(
  "# Markdown editor\nWrite markdown text and see it load in real time\n\n## Add images\n![Dogs](https://i.ibb.co/8D2nKzB/ts1jpcw2gvr01.jpg)\n\n## Emphatize text\nEither **bold** or *italic*\n\nChange text\n\nChange the type of text of any line between normal, Header 1, Header 2, Header 3 or Header 4"
);
const color = ref("white");

function hanldeUpdate(text: string) {
  mdText.value = text;
}
</script>

<template>
  <MarkdownEditor :text="mdText" :color="color" @update="hanldeUpdate" />
  <HTMLRender v-model:text="mdText" :color="color" />
</template>

<style lang="stylus">
body
  width: 100%
  margin: 0 auto
  padding: 0
  background: #b462e4

#app
  width: 98.6%
  margin: 0 auto
  padding: 0

</style>
