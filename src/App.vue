<script setup>
import Terminal from "./components/Terminal.vue"
import Layout from "./layouts/layout.vue"
import "./xterm.css"
</script>

<template>
  <Layout :terminal.once="terminal">
    <Terminal @toggleTerminal="update" />
  </Layout>
</template>

<script>
export default {
  data() {
    return {
      terminal: Terminal
    }
  },
  computed: {
    aku: () => {
      return "oke"
    },
  },
  methods: {
    update(event) {
      let { terminal } = event
      this.terminal = terminal
    }
  }
}
</script>

