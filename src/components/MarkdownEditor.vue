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

interface textClass {
  value: string;
  text: string;
  id: string;
}

interface textClasses {
  [name: string]: textClass
}

interface Cursor {
  start: number;
  end: number;
}

interface History {
  text: string;
  cursorPosition: Cursor;
}

let position = 0
let cursorPosition: Cursor = {
  start: 0,
  end: 0
}
const text = ref(props.text)
let historyText: History[] = [
  { text: props.text, cursorPosition }
]
const selected = ref("Normal")

const textClasses: textClasses = {
  "Normal": {
    value: "Normal",
    text: "",
    id: "n",
  },
  "Header 1": {
    value: "Header 1",
    text: "# ",
    id: "h1",
  },
  "Header 2": {
    value: "Header 2",
    text: "## ",
    id: "h2",
  },
  "Header 3": {
    value: "Header 3",
    text: "### ",
    id: "h3",
  },
  "Header 4": {
    value: "Header 4",
    text: "#### ",
    id: "h4",
  }
}

function updateCursor() {
  const target = document.getElementById("textInput") as HTMLTextAreaElement;
  if (target) {
    console.log(target.selectionStart)
    console.log(target.selectionEnd)
    cursorPosition = {
      start: target.selectionStart,
      end: target.selectionEnd
    }
  }
}

function reFocus(t: HTMLTextAreaElement) {
  t.focus()
  setTimeout(() => t.setSelectionRange(cursorPosition.start, cursorPosition.end));
}

function addHighlight(h: string) {
  const target = document.getElementById("textInput") as HTMLTextAreaElement;
  if (target) {
    const s = cursorPosition.start;
    const e = cursorPosition.end;
    const currentText = historyText[position].text
    const newText = [
      currentText.substring(0, s),
      h,
      currentText.substring(s, e),
      h,
      currentText.substring(e)
    ].join("")
    cursorPosition = {
      start: s + h.length,
      end: s + h.length
    }
    historyText = [
      ...historyText,
      { text: newText, cursorPosition }
    ]
    text.value = newText
    emitText(1)
    reFocus(target)
  }
}

function changeLine() {
  const target = document.getElementById("textInput") as HTMLTextAreaElement
  if (target) {
    const s = target.selectionStart;
    const currentText = historyText[position].text
    const firstN = currentText.substring(0, s).lastIndexOf("\n") + 1
    const lastNSearch = currentText.substring(s).indexOf("\n")
    const lastN = lastNSearch > 0 ? lastNSearch : currentText.length

    const line = currentText.substring(firstN, lastN)
    const addedText = textClasses[selected.value].text
    const searcher = /^#+ /
    const lineStart = line.match(searcher)?.[0].length ?? 0

    let newLine = "";
    if (addedText.length === lineStart) {
      newLine = line.replace(searcher, "")
    } else if (lineStart > 0) {
      newLine = line.replace(searcher, addedText)
    } else {
      newLine = addedText + line
    }

    const newText = [
      currentText.substring(0, firstN),
      newLine,
      currentText.substring(lastN),
    ].join("");

    cursorPosition = {
      start: s + (addedText.length - lineStart),
      end: s + (addedText.length - lineStart)
    }
    historyText = [
      ...historyText,
      { text: newText, cursorPosition }
    ]
    text.value = newText
    emitText(1)
    reFocus(target)
  }
}

function changeLineKey(h: string) {
  selected.value = h
  setTimeout(() => changeLine(),)
}

function un_reDo(t: number) {
  const next = historyText[position + t]
  if (next) {
    emitText(t)
  }
}

function updateText(): void {
  historyText = [
    ...historyText,
    { text: text.value, cursorPosition }
  ]
  emitText(1)
}

function emitText(val: number) {
  position += val
  text.value = historyText[position].text
  cursorPosition = historyText[position].cursorPosition

  const target = document.getElementById("textInput") as HTMLTextAreaElement;
  if (target) {
    reFocus(target)
  }
  emits("update", text.value)
}
</script>

<template>
  <div id="containerMarkdown">
    <div id="buttons">
      <a href="#" class="editorButton" @click="addHighlight('**'), updateText()">
        <b>B</b>
      </a>
      <a href="#" class="editorButton" @click="addHighlight('*')">
        <i>I</i>
      </a>
      <select name="Text" id="textSelection" v-model="selected" @change="changeLine()">
        <option v-for="textClass in textClasses" :key="textClass.id">{{ textClass.value }}</option>
      </select>
    </div>
    <textarea
      id="textInput"
      rows="15"
      cols="100"
      placeholder="Enter some text..."
      v-model="text"
      @input="updateText"
      @keyup.left="updateCursor"
      @keyup.right="updateCursor"
      @keyup.up="updateCursor"
      @keyup.down="updateCursor"
      @mouseup="updateCursor"
      @keydown.ctrl.z.exact.prevent="un_reDo(-1)"
      @keydown.ctrl.y.exact.prevent="un_reDo(1)"
      @keydown.ctrl.b.exact.prevent="addHighlight('**')"
      @keydown.ctrl.i.exact.prevent="addHighlight('*')"
      @keydown.ctrl.1.exact.prevent="changeLineKey('Header 1')"
      @keydown.ctrl.2.exact.prevent="changeLineKey('Header 2')"
      @keydown.ctrl.3.exact.prevent="changeLineKey('Header 3')"
      @keydown.ctrl.4.exact.prevent="changeLineKey('Header 4')"
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