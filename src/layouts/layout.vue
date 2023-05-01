<template>
  <div class="navbar_container">
    <button class="btn_terminal" @click="updateParent">Show Terminal</button>
  </div>
  <div class="container">
    <slot></slot>
  </div>
</template>
<script>
import { Terminal } from "xterm"
import "./layout.css"
export default {
  name: 'Layout',
  props: {
    terminal: { type: Terminal, required: true }
  },
  methods: {
    updateParent() {
      let container = document.getElementById("container")
      if (container.hidden != true) {
        container.hidden = true
      } else {
        container.hidden = false
      }
    }
  }
}
</script>
