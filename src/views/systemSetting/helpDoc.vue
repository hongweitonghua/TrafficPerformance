<template>
    <section>
        <el-row  v-if="$userInfo&&$userInfo.roleType&&$userInfo.roleType=='R_ADMIN'"> 
            <el-col :span= "6"  class="top">
                <div class="grid-content"> 
                    <!-- <input type="file"  @change="upload">  -->
                    <el-upload
                    :action="murl"
                    :data = "data"
                    :on-preview="handlePreview"
                    :on-remove="handleRemove"
                    :headers="headers"
                    :before-upload="beforeUpload"
                    :http-request="upload">
                    <el-button size="small" type="primary">点击上传</el-button>
                    <div slot="tip" class="el-upload__tip">请上传10m 以内的pdf文件</div>
                    </el-upload>   
                </div>
            </el-col>
        </el-row>

        <el-row type="flex" >
            <el-col :span= "6" class="top"> 
                <div class="grid-content"> 
                    <el-button @click="download">下载文档</el-button> 
                </div> 
            </el-col>
        </el-row>

        <el-row type="flex" justify="center" v-if="!loading">
            <el-col :span= "6"> 
                    <div class="grid-content" > 
                         <el-button id="prev" @click="onPrevPage" type="primary">上一页</el-button> 
                    </div> 
            </el-col>
            <el-col :span= "4">
                <div align="center">
                    <span><span >{{ page_count}}</span> / <span  >{{ page_num }}</span></span>
                </div>
            </el-col>
            <el-col :span= "6"> 
                    <div class="grid-content" align="right"> 
                           <el-button id="next" @click="onNextPage" type="primary">下一页</el-button>
                    </div> 
            </el-col>
        </el-row>

        <el-row type="flex"  v-loading="loading"
            element-loading-text="拼命加载中">
            <el-col class = "pdf">
                <div class="grid-content" align="center">
                    <canvas id="the-canvas" > </canvas>
                </div>
            </el-col>
        </el-row>





    </section> 
</template>

<script>
    PDFJS.workerSrc = 'https://mozilla.github.io/pdf.js/build/pdf.worker.js';

export default {
    data() {
        return {
            loading:true,
            pageNum:1,
            pageRendering:false,
            pdfDoc:null,
            pageNumPending:null,
            scale : 1.5,
            canvas : {},
            ctx:{},
            page_num:0,
            page_count:0,
            murl:this.Constant.ajaxAddress+this.Constant.upload,
            durl:this.Constant.ajaxAddress+this.Constant.download,
            data:{},
            headers:{'Access-Control-Allow-Origin':'*','Content-Type':'multipart/form-data'}
        }
    },

    methods:{
        upload(event){
            console.log(event);
           // var pdfFile= event.target.files[0];
           var pdfFile= event.file;
            if(!pdfFile) return ;
            console.log(pdfFile);
            this.data.token = this.$token;
            
            var fd = new FormData();
            fd.append('file',pdfFile);
            fd.append('token', this.$token);
            var _this = this;
            this.$http.post(this.murl,fd,{emulateJSON: true}).then(res =>{
                _this.$message('上传成功');
                 _this.loadPdf();
                console.log(res);
            },err =>{ 
                _this.$message('上传失败');console.log('err');})
        },
        handleRemove(file, fileList) {
            console.log(file, fileList);
        },
        handlePreview(file) {
            console.log(file);
        },
        beforeUpload(file){
            console.log('before upload',this.headers);
            
            this.data.token = this.$token;   
        },
        loadPdf(){
            this.pageNum=1;
            this.pageRendering=false;
            this.pdfDoc=null;
            this.pageNumPending=null;
            var _this = this;
            PDFJS.getDocument(this.durl).then(function(pdfDoc_) {
                _this.loading= false;
                console.log('pdfDoc',pdfDoc_);
                _this.pdfDoc = pdfDoc_;
                console.log('pdfDoc',_this.pdfDoc);
                //document.getElementById('page_count').textContent = _this.pdfDoc.numPages;
                _this.page_num = _this.pdfDoc.numPages;
                // Initial/first page rendering
                _this.renderPage(_this.pageNum);
            });
        },
        renderPage(num) {
            var _this = this;
            this.pageRendering = true;
            // Using promise to fetch the page
            this.pdfDoc.getPage(num).then(function(page) {
                var viewport = page.getViewport(_this.scale);
                _this.canvas.height = viewport.height;
                _this.canvas.width = viewport.width;

                // Render PDF page into canvas context
                var renderContext = {
                canvasContext: _this.ctx,
                viewport: viewport
                };
                var renderTask = page.render(renderContext);

                // Wait for rendering to finish
                renderTask.promise.then(function() {
                _this.pageRendering = false;
                if (_this.pageNumPending !== null) {
                    // New page rendering is pending
                    renderPage(_this.pageNumPending);
                    _this.pageNumPending = null;
                }
                });
            });
            // Update page counters
             this.page_count =this.pageNum;
        },
        queueRenderPage(num) {
            if (this.pageRendering) {
                this.pageNumPending = num;
            } else {
                this.renderPage(num);
            }
        },
        onPrevPage() {
            if (this.pageNum <= 1) {
                return;
            }
            this.pageNum--;
            this.queueRenderPage(this.pageNum);
        },
        onNextPage() {
            if (this.pageNum >= this.pdfDoc.numPages) {
                return;
            }
            this.pageNum++;
            this.queueRenderPage(this.pageNum);
        },
        download(){
            window.location.href=this.durl;
        }
    },
    mounted:function(){
        this.canvas = document.getElementById('the-canvas');
        this.ctx = this.canvas.getContext('2d');
        this.loadPdf();
    }

 }
</script>

<style scoped lang="scss">
    .pdf{
        border:1px solid black;
    }
    .grid-content{
        border-radius: 8px;
        min-height: 50px;
    }
    .top{
        margin-top: 50px
    }
</style>


