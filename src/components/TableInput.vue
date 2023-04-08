<template>
  <div>
    <div class="row">
      <div class="col-md-6">
        <b>Insert markdown or CSV table</b>
        <textarea class="form-control" rows="10" cols="100" v-model="input" @input="parseInput" placeholder="Paste your CSV or Markdown table here"></textarea>
      </div>
      <div class="col-md-6">
        <b>Font size <small class="text-muted">{{ fontSize }}px</small></b>
        <input v-model="fontSize" @input="sendConfig" class="form-range mb-3" type="range" min="0" max="100" step="1"/>

        <b>Cell Padding <small class="text-muted">{{ cellPadding }}px</small></b>
        <input v-model="cellPadding" @input="sendConfig" class="form-range mb-3" type="range" min="0" max="100" step="1"/>

        <b>Padding <small class="text-muted">{{ padding }}px</small></b>
        <input v-model="padding" @input="sendConfig" class="form-range mb-3" type="range" min="0" max="100" step="1"/>

        <!-- <b>Shadow <small class="text-muted">{{ shadow }}px</small></b>
        <input v-model="shadow" @input="sendConfig" class="form-control mb-3" type="range" min="0" max="100" step="1"/>         -->

        <!-- <b>Logo</b>
        <input v-model="logo" @input="sendConfig" class="form-control mb-3" type="text"/> -->

        <b>Watermark</b>
        <input v-model="watermark" @input="sendConfig" class="form-control mb-3" type="text"/>

        <div class="color-palatte" v-bind:style="`background:#c850c0;
background:-webkit-linear-gradient(45deg,#4158d0,#c850c0);
background:-o-linear-gradient(45deg,#4158d0,#c850c0);
background:-moz-linear-gradient(45deg,#4158d0,#c850c0);
background:linear-gradient(45deg,#4158d0,#c850c0);`"
          @click="mainColor = '#c850c0';secondaryColor = '#4158d0';degree = '45deg'; sendConfig()"
        ></div>

        <div class="color-palatte" v-bind:style="`background:#0093E9;
background:-webkit-linear-gradient(45deg,#80D0C7,#0093E9);
background:-o-linear-gradient(45deg,#80D0C7,#0093E9);
background:-moz-linear-gradient(45deg,#80D0C7,#0093E9);
background:linear-gradient(45deg,#80D0C7,#0093E9);`"
          @click="mainColor = '#0093E9';secondaryColor = '#80D0C7';degree = '160deg'; sendConfig()"
        ></div>        

        <div class="color-palatte" v-bind:style="`background:#FBAB7E;
background:-webkit-linear-gradient(45deg,#F7CE68,#FBAB7E);
background:-o-linear-gradient(45deg,#F7CE68,#FBAB7E);
background:-moz-linear-gradient(45deg,#F7CE68,#FBAB7E);
background:linear-gradient(45deg,#F7CE68,#FBAB7E);`"
          @click="mainColor = '#FBAB7E';secondaryColor = '#F7CE68';degree = '62deg'; sendConfig()"
        ></div>       
        
        <div class="color-palatte" v-bind:style="`background:#8BC6EC;
background:-webkit-linear-gradient(45deg,#8BC6EC,#8BC6EC);
background:-o-linear-gradient(45deg,#8BC6EC,#8BC6EC);
background:-moz-linear-gradient(45deg,#8BC6EC,#8BC6EC);
background:linear-gradient(45deg,#8BC6EC,#8BC6EC);`"
          @click="mainColor = '#8BC6EC';secondaryColor = '#8BC6EC';degree = '135deg'; sendConfig()"
        ></div>               

        <div class="color-palatte" v-bind:style="`background:#FBDA61;
background:-webkit-linear-gradient(45deg,#FF5ACD,#FBDA61);
background:-o-linear-gradient(45deg,#FF5ACD,#FBDA61);
background:-moz-linear-gradient(45deg,#FF5ACD,#FBDA61);
background:linear-gradient(45deg,#FF5ACD,#FBDA61);`"
          @click="mainColor = '#FBDA61';secondaryColor = '#FF5ACD';degree = '45deg'; sendConfig()"
        ></div>     
        <div class="color-palatte" v-bind:style="`background:#FAACA8;
background:-webkit-linear-gradient(45deg,#DDD6F3,#FAACA8);
background:-o-linear-gradient(45deg,#DDD6F3,#FAACA8);
background:-moz-linear-gradient(45deg,#DDD6F3,#FAACA8);
background:linear-gradient(45deg,#DDD6F3,#FAACA8);`"
          @click="mainColor = '#FAACA8';secondaryColor = '#DDD6F3';degree = '75deg'; sendConfig()"
        ></div>   

      </div>
    </div>
  </div>
</template>
<script>
import Papa from "papaparse";
import MarkdownIt from "markdown-it";

export default {
  data() {
    return {
      input: '',
      cellPadding: 20,
      padding: 50,
      fontSize: 20,
      shadow: 20,
      watermark: 'beautifootable.com',
      mainColor: '#c850c0',
      secondaryColor: '#4158d0',
      degree: '45deg'
    }
  },
  mounted(){
    this.input = `| Beautiful Table    | Why? |
| -------- | ------- |
| #1  | Beautiful table for your blog or article    |
| #2  | Beautiful table for your social media post     |
| #3    | Beautiful table for your everything    |`
    this.parseInput()
    this.sendConfig()
  },
  methods: {
    extractTableDataFromTokens(tokens) {
      const tableData = [];
      let headers = [];
      let rowData = [];

      for (const token of tokens) {
        if (token.type === "table_open") {
          headers = [];
          rowData = [];
        } else if (token.type === "thead_open" || token.type === "tbody_open") {
          rowData = [];
        } else if (token.type === "tr_open") {
          rowData = [];
        } else if (token.type === "th_close") {
          headers.push(token.content.trim());
        } else if (token.type === "td_close") {
          rowData.push(token.content.trim());
        } else if (token.type === "tr_close") {
          if (headers.length > 0) {
            tableData.push(headers.slice());
          }
          if (rowData.length > 0) {
            tableData.push(rowData.slice());
          }
        } else if (token.type === "table_close") {
          break;
        }
      }

      return tableData;
    },
    parseInput() {
      if (this.isHTMLTable(this.input)) {
        this.parseHTML();
      } else if (this.isCSV(this.input)) {
        this.parseCSV();
      } else {
        this.parseMarkdown();
      }
    },
    sendConfig() {
      let config = { 
        fontSize: this.fontSize + 'px',
        logo: this.logo,
        watermark: this.watermark,
        cellPadding: this.cellPadding + 'px',
        padding: this.padding + 'px',
        mainColor: this.mainColor,
        secondaryColor: this.secondaryColor,
        degree: this.degree,
      }
      this.$emit("update:config", config);
    },    
    isHTMLTable(input) {
      const htmlTableRegex = /<table[^>]*>([\s\S]*?)<\/table>/i;
      return htmlTableRegex.test(input);
    },

    isCSV(input) {
      return input.includes(",") && !input.includes("|");
    },
    parseCSV() {
      const parsed = Papa.parse(this.input, { header: true });
      this.headers = parsed.meta.fields;
      this.$emit("update:headers", this.headers);
      this.$emit("update:tableData", parsed.data);
    },
    parseMarkdown() {
      const md = new MarkdownIt();
      const tableData = [];
      let headers = [];
      let rowData = [];

      md.renderer.rules.th_close = (tokens, idx) => {
        headers.push(tokens[idx - 1].content.trim());
        return "";
      };

      md.renderer.rules.td_close = (tokens, idx) => {
        rowData.push(tokens[idx - 1].content.trim());
        return "";
      };

      md.renderer.rules.tr_close = () => {
        if (headers.length > 0) {
          this.$emit("update:headers", headers);
          headers = [];
        }
        if (rowData.length > 0) {
          tableData.push(rowData.slice());
          rowData = [];
        }
          return "";
      };
      md.render(this.input);
      this.$emit("update:tableData", tableData);
    },
    parseHTML() {
      const parser = new DOMParser();
      const doc = parser.parseFromString(this.input, 'text/html');
      const headers = [];
      const tableData = [];

      const table = doc.querySelector('table');
      if (!table) return;

      const thead = table.querySelector('thead');
      const tbody = table.querySelector('tbody');

      if (thead) {
        thead.querySelectorAll('tr th').forEach((element) => {
          headers.push(element.textContent.trim());
        });
      }

      if (tbody) {
        tbody.querySelectorAll('tr').forEach((rowElement) => {
          const rowData = [];
          rowElement.querySelectorAll('td').forEach((cellElement) => {
            rowData.push(cellElement.textContent.trim());
          });
          tableData.push(rowData);
        });
      }

      this.$emit('update:headers', headers);
      this.$emit('update:tableData', tableData);
    },  
  },
};
</script>

<style>
.color-palatte {
  width:30px;
  height:30px;
  border-radius: 10px;
  cursor: pointer;
  display: inline-block;
  margin: 4px;
}
</style>