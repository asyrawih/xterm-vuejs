<template>
  <div class="box">
    <div id="container">
      <div id="terminal"></div>
    </div>
  </div>
</template>

<script>
import { Terminal } from 'xterm';
import { AttachAddon } from 'xterm-addon-attach';
import { FitAddon } from 'xterm-addon-fit';
import { WebLinksAddon } from 'xterm-addon-web-links';
import "./terminal.css"
export default {
  name: 'TerminalView',
  data() {
    return {
      ws: WebSocket,
      terminal: new Terminal
    }
  },
  created() {
    var protocol = (location.protocol === "https:") ? "wss://" : "ws://";
    var url = protocol + "localhost:3000" + "/ws"
    var ws = new WebSocket(url);
    this.ws = ws
  },
  methods: {
    closeConnection() {
      this.ws.close()
      window.close()
    },
    loadConnection() {
      let ws = this.ws
      var terminal = new Terminal({
        screenKeys: true,
        useStyle: true,
        cursorBlink: true,
        screenReaderMode: true,
        fontFamily: "FiraCode Nerd Font",
        cols: 128,
      });
      this.terminal = terminal
      terminal.open(document.getElementById("terminal"));

      var attachAddon = new AttachAddon(this.ws, { bidirectional: true });

      var fitAddon = new FitAddon();
      terminal.loadAddon(fitAddon);
      fitAddon.fit()

      var webLinkAddon = new WebLinksAddon()
      terminal.loadAddon(webLinkAddon)

      ws.onclose = () => {
        terminal.clear()
        terminal.writeln("Byeee")
      }

      ws.onopen = () => {
        terminal.loadAddon(attachAddon)
        terminal._initialized = true;
        terminal.focus();
        terminal.onResize((event) => {
          var rows = event.rows;
          var cols = event.cols;
          var size = JSON.stringify({ cols: cols, rows: rows + 1 });
          var send = new TextEncoder().encode("\x01" + size);
          ws.send(send);
        })

        terminal.onTitleChange(function (event) {
          console.log(event)
        });

        window.onresize = function () {
          fitAddon.fit();
        };
      }
    }
  },
  mounted() {
    this.loadConnection()
    this.$emit("toggleTerminal", { terminal: this.terminal })
  },
  unmounted() {
    this.terminateWS()
  }
}
</script>
<style></style>
