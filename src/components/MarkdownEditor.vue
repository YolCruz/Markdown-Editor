<script setup lang="ts">
import { emit } from "process";
import { ref, computed } from "vue"
const props = defineProps<{
  text: string,
  color: string,
}>();

const emits = defineEmits<{
  (event: "update", val: string): void
}>()

const text = ref(props.text)

function emitText() {
  emits("update", text.value)
}
</script>

<template>
  <div id="containerMarkdown">
    <textarea
      id="textInput"
      rows="15"
      cols="100"
      placeholder="Enter some text..."
      v-model="text"
      @input="emits('update', text)"
    />
  </div>
</template>

<style scoped lang="stylus">
#containerMarkdown
  margin: 30px auto
  display: flex
  flex-direction: column
  background: v-bind("props.color")
  border-radius: 20px
  box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px;

  #buttons
    width: 96%
    border: 1px solid #dcdcdc
    padding: 2px
    margin: 25px auto 0px
    display: flex
    align-items: center

    #textSelection
      border: 1px solid #dcdcdc;
      border-radius: 3px;
      background: #ffffff;
      appearance: none
      width: 120px
      padding: 6px 8px
      font-family:Courier New;
      font-size: 1.1rem
      cursor:pointer
    #textSelection:hover
    #textSelection:focus
      background:linear-gradient(to bottom, #f6f6f6 5%, #ffffff 100%)
	    background-color:#f6f6f6  

  textarea
    width: 96%
    border-radius: 10px
    margin: 10px auto

.editorButton
	box-shadow:inset 0px 1px 0px 0px #ffffff
	background:linear-gradient(to bottom, #ffffff 5%, #f6f6f6 100%)
	background-color:#ffffff
	border-radius:3px
	border:1px solid #dcdcdc
	display:inline-block
	cursor:pointer
	color:#666666
	font-family:Courier New;
	font-size:1.1rem
	padding:6px 8px
	text-decoration:none
	text-shadow:0px 1px 0px #ffffff

.editorButton:hover
	background:linear-gradient(to bottom, #f6f6f6 5%, #ffffff 100%);
	background-color:#f6f6f6;

.editorButton:active
	position:relative;
	top:1px;

@media only screen and (max-width: 600px)
  #containerMarkdown
    width: 100%


@media only screen and (min-width: 601px)
  #containerMarkdown
    width: 75%

</style>