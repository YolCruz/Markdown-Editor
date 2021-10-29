<script setup lang="ts">
import { computed } from "vue";
import marked from "marked";
import DOMPurify from "dompurify"

const props = defineProps<{
  text: string,
  color: string,
}>()

const parsed = computed(() => DOMPurify.sanitize(marked(props.text)));
</script>

<template>
  <div id="containerHTML">
    <div id="HTMLRender" v-html="parsed" />
  </div>
</template>

<style lang="stylus">
#containerHTML
  display: flex
  justify-content: center
  margin: 50px auto
  border: 1px solid black
  border-radius: 12px
  background: v-bind("props.color")
  box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px;

#HTMLRender
  width: 100%
  padding: 20px
  border: 1px solid black
  border-radius: 12px
  img
    width: 20rem
    max-width: 500px
  h1
    font-size: 1.4rem
  h2
    font-size: 1.3rem
  h3
    font-size: 1.2rem
  h4
    font-size: 1.1rem
  p
    font-size: 1rem

@media only screen and (max-width: 600px)
  #containerHTML
    width: 100%

@media only screen and (min-width: 601px)
  #containerHTML
    width: 85%

</style>