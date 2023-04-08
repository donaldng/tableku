<template>
  <div>
    <b>Beautiful table</b>
    <div ref="tableContainer" class="table-container" :style="tableStyle">
      <div class="limiter">
        <div class="container-table100" 
        :style="`padding: ${config.padding ? config.padding : '40px'}
background:${config.mainColor};
background:-webkit-linear-gradient(${config.degree},${config.secondaryColor},${config.mainColor});
background:-o-linear-gradient(${config.degree},${config.secondaryColor},${config.mainColor});
background:-moz-linear-gradient(${config.degree},${config.secondaryColor},${config.mainColor});
background:linear-gradient(${config.degree},${config.secondaryColor},${config.mainColor});
        `">
          <div class="table100">
            <table :style="`border-radius: ${config.padding === '0px' ? '0px':'10px'};box-shadow: rgba(0, 0, 0, 0.35) 0px 5px ${config.shadow === '0px' ? '0px':'20px'};`">
              
              <thead>
              <tr class="table100-head">
                <th 
                :style="`font-size: ${config.fontSize ? config.fontSize : '14px'}; padding: ${config.cellPadding ? config.cellPadding : '10px'}`"
                style="padding-left: 30px !important; padding-right:30px !important;" v-for="(item, key) in headers" v-bind:key="key">{{ item }}</th>
              </tr>
              </thead>
              <tbody>
                <tr v-for="(row, rowIndex) in tableData" :key="rowIndex">
                  <td 
                  :style="`font-size: ${config.fontSize ? config.fontSize : '14px'}; padding: ${config.cellPadding ? config.cellPadding : '10px'}`"
                  style="padding-left: 30px !important; padding-right:30px !important;" v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
                </tr>
              </tbody>
            </table>
            <div class="wrap-table100">
              <!-- <p class="watermark text-white small fw-400" style="font-size: 14px">beautifootable.com</p> -->
              <p v-if="config.watermark" class="watermark text-white small fw-400" style="font-size: 14px">{{ config.watermark }}</p>
              <img v-if="config.logo" src="@/assets/howuku-logo.png" width="120px" class="watermark mt-3"/>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="text-center">
      <button class="btn btn-primary text-center m-4" @click="downloadimage">Download Image</button>
      <button class="btn btn-primary text-center m-4" @click="copy2clipboard">Copy to Clipboard</button>
    </div>
  </div>
</template>


<script>
/* global ClipboardItem */
import domtoimage from 'dom-to-image';

export default {
  props: {
    config: {
      type: Object,
      default: () => ({}),
    },    
    tableData: {
      type: Array,
      default: () => [],
    },
    headers: {
      type: Array,
      default: () => [],
    },    
    tableStyle: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      imageBlob: null,
    };
  },
  methods: {
    async downloadimage() {

      domtoimage.toBlob(this.$refs.tableContainer, { quality: 1 })
      .then(function (blob) {
          const url = URL.createObjectURL(blob);
          const link = document.createElement("a");
          link.href = url;
          link.download = "table-image.png";
          link.click();
          URL.revokeObjectURL(url);           
      });


      // const canvas = await html2canvas(this.$refs.tableContainer);
      //   canvas.toBlob(blob => {
      //   this.imageBlob = blob;
      //   this.$emit("update:imageBlob", this.imageBlob);
      //   const url = URL.createObjectURL(this.imageBlob);
      //   const link = document.createElement("a");
      //   link.href = url;
      //   link.download = "table-image.png";
      //   link.click();
      //   URL.revokeObjectURL(url);        
      // });
    },
    copy2clipboard() {

      domtoimage.toBlob(this.$refs.tableContainer, { quality: 1 })
      .then(async function (blob) {
          if (!navigator.clipboard) {
            console.error("Clipboard API not supported in this browser");
            return;
          }

          try {
            const item = new ClipboardItem({ [blob.type]: blob });
            await navigator.clipboard.write([item]);
          } catch (err) {
            console.error("Error copying image to clipboard", err);
          }          
      });
    },    
  },
};
</script>

<style>
*{margin:0;padding:0;box-sizing:border-box}body,html{height:100%;font-family:sans-serif}a{margin:0;transition:all .4s;-webkit-transition:all .4s;-o-transition:all .4s;-moz-transition:all .4s}a:focus{outline:none!important}a:hover{text-decoration:none}h1,h2,h3,h4,h5,h6{margin:0}p{margin:0}ul,li{margin:0;list-style-type:none}
.limiter{width:100%;margin:0 auto}
.container-table100{
  display:-webkit-box;
  display:-webkit-flex;
  display:-moz-box;
  display:-ms-flexbox;
  /* display:flex;
  align-items:center;
  justify-content:center;
  flex-wrap:wrap;
  flex-direction: column; */
}
table{border-spacing:1;border-collapse:collapse;background:#fff;overflow:hidden;width:100%;margin:0 auto;position:relative}table *{position:relative}table td,table th{padding-left:8px}table thead tr{height:60px;background:#36304a}table tbody tr{height:50px}table tbody tr:last-child{border:0}table td,table th{text-align:left}table td.l,table th.l{text-align:right}table td.c,table th.c{text-align:center}table td.r,table th.r{text-align:center}
.table100-head th{font-family:system-ui,-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Oxygen,Ubuntu,Cantarell,'Fira Sans','Droid Sans','Helvetica Neue',sans-serif;color:#fff;line-height:1.2;font-weight:unset}tbody tr:nth-child(even){background-color:#f5f5f5}tbody tr{font-family:system-ui,-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Oxygen,Ubuntu,Cantarell,'Fira Sans','Droid Sans','Helvetica Neue',sans-serif;font-size:15px;color:gray;line-height:1.2;font-weight:unset}tbody tr:hover{color:#555;background-color:#f5f5f5;cursor:pointer}
@media screen and (max-width:992px){table{display:block}table>*,table tr,table td,table th{display:block}table thead{display:none}table tbody tr{height:auto;padding:37px 0}table tbody tr td{padding-left:40%!important;margin-bottom:24px}table tbody tr td:last-child{margin-bottom:0}table tbody tr td:before{font-family:system-ui,-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,Oxygen,Ubuntu,Cantarell,'Fira Sans','Droid Sans','Helvetica Neue',sans-serif;font-size:14px;color:#999;line-height:1.2;font-weight:unset;position:absolute;width:40%;left:30px;top:0}

tbody tr{font-size:14px}

}@media(max-width:576px){.container-table100{padding-left:15px;padding-right:15px}}

.watermark {
  margin-top:4px;
  margin-right: 8px;
  float:right;
  font-size: 2rem;
  color: white;
  z-index: 1;
  pointer-events: none;
  bottom: 110px;
  right: calc(50% - 100px);
}

/* background:#c850c0;
  background:-webkit-linear-gradient(45deg,#4158d0,#c850c0);
  background:-o-linear-gradient(45deg,#4158d0,#c850c0);
  background:-moz-linear-gradient(45deg,#4158d0,#c850c0);
  background:linear-gradient(45deg,#4158d0,#c850c0); */

</style>


