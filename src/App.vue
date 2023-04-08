<template>
  <div id="app" class="container p-4">
    <div class="pb-4">
      <h1 class="text-center fw-bold">Beautifoo Table</h1>
      <p class="text-center text-muted">Turn Boring Table into Beautiful Table</p>
    </div>
    
    <br>
    <TableInput @update:tableData="updateTableData" @update:headers="updateHeaders" @update:config="updateConfig" />
    <br>
    <TableImage
      :config="config"
      :headers="headers"
      :tableData="tableData"
      :tableStyle="tableStyle"
      @update:imageBlob="updateImageBlob"
    />

    <br>

    <div class="row">
      <div class="col-md-6">
        <h2 class="fw-bold">FAQs</h2>
        <br>
        <p class="fw-bold mb-1">Why beautiful table?</p>
        <p>The real question is... why not? I created this because <a href="https://howuku.com/blog" target="_blank">our blog</a> is hosted on Webflow and it kinda sucked since the Webflow rich-text editor doesn't support table.</p>
        <br>
        <p class="fw-bold mb-1">Is it free?</p>
        <p>Yes, it is forever free. If you like the service, do share it with your friends and check out Howuku.</p>    
        <br>        
        <p class="fw-bold mb-1">This site look kind of scrappy</p>
        <p>Yeah, I made it in a day so...</p>
      </div>
      <div class="col-md-6">
        <h2 class="fw-bold">&nbsp;</h2>
        <br>
        <p class="fw-bold mb-1">What is Howuku?</p>
        <p>Howuku is an all-in-one web analytics and optimization tool. We have really cool features such as Analytics, Heatmap, A/B Testing, Event Tracking, and etc. Do check it out!</p>
      </div>      
    </div>
    <footer class="mt-5 py-5 text-center">
      <b>Made with ❤️ by <a href="https://howuku.com/?utm_source=beautifultable" target="_blank"><img class="text-center pb-1 pl-2" src="https://uploads-ssl.webflow.com/62679692b1466017b8eae590/62679e471c0eec3abda27d26_logo.svg" width="80px" /></a></b>
    </footer>
  </div>
</template>

<script>
import TableInput from "./components/TableInput.vue";
import TableImage from "./components/TableImage.vue";

export default {
  name: "App",
  components: {
    TableInput,
    TableImage,
  },
  data() {
    return {
      config: {},
      headers: [],
      tableData: [],
      tableStyle: {
        /* Add your desired styles for the table container here */
      },
      imageBlob: null,
    };
  },
  methods: {
    updateHeaders(newHeaders) {
      this.headers = newHeaders;
    },
    updateConfig(newConfig) {
      this.config = newConfig;
    },    
    updateTableData(newData) {
      this.tableData = newData;
    },
    updateImageBlob(newBlob) {
      this.imageBlob = newBlob;
    },
    downloadImage() {
      const url = URL.createObjectURL(this.imageBlob);
      const link = document.createElement("a");
      link.href = url;
      link.download = "table-image.png";
      link.click();
      URL.revokeObjectURL(url);
    },
  },
};
</script>
