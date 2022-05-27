### vue-pdf

- ##### 安装

  ```
  npm install --save vue-pdf
  ```

- ##### 使用

  基本使用

  ```vue
  <template>
  	<pdf :src="pdfSrc"></pdf>
  </template>
  <script>
  // 引入pdf
  import pdf from "vue-pdf";
  
  export default {
    components: {
      pdf,
    },
    data() {
      return {
        pdfSrc: "testPdf.pdf",
        pageNum: 0,
      };
    },
    mounted(){
      this.loadPdfHandler();    
    },
    methods: {
      loadPdfHandler() {
        this.pdfSrc.promise.then((pdf) => {
          // 获取pdf全部页数
          this.pageNum = pdf.numPages;
        });
      },
    },
  };
  </script>
  ```

  带上下页按钮，对**page**属性进行操作即可

  ```vue
  <template>
  	<pdf :src="pdfSrc" :page="curPage"></pdf>
       <div>
         <el-button
           type="primary"
           @click="
             () => {
                this.curPage--;
              }
           "
           :disabled="curPage <= 1"
           >上一页</el-button
         >
         <el-button
           type="primary"
           @click="
             () => {
               this.curPage++;
             }
           "
           :disabled="curPage >= pageNum"
           >下一页</el-button
         >
       </div>
  </template>
  <script>
  // 引入pdf
  import pdf from "vue-pdf";
  
  export default {
    components: {
      pdf,
    },
    data() {
      return {
        pdfSrc: "testPdf.pdf",
        pageNum: 0,
      };
    },
    mounted(){
      this.loadPdfHandler();    
    },
    methods: {
      loadPdfHandler() {
        this.pdfSrc.promise.then((pdf) => {
          // 获取pdf全部页数
          this.pageNum = pdf.numPages;
        });
      },
    },
  };
  </script>
  ```

  在窗口中展示，只需要使用div包裹住pdf组件，同时给div加上overflow: scroll;属性且设置一个固定高度即可

  ```html
  <div style="height: 500px;overflow-y:scroll;">
  	<pdf
          :src="pdfSrc"
          v-for="(item, idx) in pageNum"
          :key="idx"
          :page="item"
  	></pdf>
  </div>
  ```

- 中文无法显示问题

  如果pdf有中文，则可能无法加载，需要引入**CMapReaderFactory**

  ```js
  import CMapReaderFactory from "vue-pdf/src/CMapReaderFactory.js";
  
  // 在执行loadPdfHandler前使用CMapReaderFactory
  loadPdfHandler() {
    this.pdfSrc = pdf.createLoadingTask({
      url: this.pdfSrc,
      CMapReaderFactory,
    });
    this.pdfSrc.promise.then((pdf) => {
      this.pageNum = pdf.numPages;
      console.log(this.pageNum, "111");
    });
  }
  ```

- 第二次打开时无法展示

  这是引入**CMapReaderFactory**导致的，是缓存问题，因此每次打开都需要删除缓存，同时需要找到**vue-pdf/src/CMapReaderFactory.js**中加上以下代码

  ```js
  loadPdfHandler() {
    this.pdfSrc = pdf.createLoadingTask({
      url: this.pdfSrc,
      CMapReaderFactory,
    });
    // 加载完PDF后对缓存进行清除
    for (var key in require.cache) {
      if (key.indexOf("bcmap") >= 0) {
        delete require.cache[key];
      }
    }
    this.pdfSrc.promise.then((pdf) => {
      this.pageNum = pdf.numPages;
      console.log(this.pageNum, "111");
    });
  }
  ```

  ```js
  delete require.cache[require.resolve('./buffer-loader!pdfjs-dist/cmaps/' + query.name + '.bcmap')];
  ```

- 签章问题

  当pdf中带有签章时，会导致签章加载不出来，此时需要在**pdfjs-dist**中找到**'es5/build/pdf.worker.js'**文件，并将以下代码注释

  ```js
  if (data.fieldType === "Sig") {
    data.fieldValue = null;
    // 注释下面这部分代码即可
    // _this3.setFlags(_util.AnnotationFlag.HIDDEN);
  }
  ```

