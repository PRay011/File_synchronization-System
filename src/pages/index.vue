<template>
    <navigationBar></navigationBar>
    <div class="container">
        <div class="local">
            <el-text class="mx-1" type="primary" style="margin-left: 0%;" size="large">本地共享文件夹：</el-text>
            <div style="margin: 7px 0" />
            <el-scrollbar max-height="600px" style="margin-top: 10px;">
                <el-upload
                v-model:file-list="localFiles"
                class="upload-demo"
                action="#"
                :http-request="sendFiles"
                :on-preview="localPreview"
                :on-remove="localRemove"
                list-type="picture"
            >
            <el-button type="primary">Click to add</el-button>    
            </el-upload>
            </el-scrollbar>
        </div>
        <div class="remote">
            <el-text class="mx-1" type="primary" style="margin-left: 0%;" size="large">远程同步文件夹：</el-text>
            <div style="margin: 7px 0" />
            <el-button type="primary" @click="getFiles">Click to add</el-button>
            <el-text class="mx-1" type="primary" style="margin-left: 37%;">Automatic synchronization:</el-text>
            <el-switch v-model="autoSync" style="margin-left: 1%;" @change="Automatic"/>
            <el-scrollbar max-height="600px" style="margin-top: 2px;">  
                <div class="files" v-for="remoteFile in remoteFiles" :key="remoteFile.device_id">
                    <el-icon style="position: relative; margin-left:96%;"><Close @click="remoteRemove(remoteFile)" /></el-icon>
                    <div style="margin: 1px 0" />
                    <el-text class="mx-1" type="primary" style="margin-left: 5px;">device_id:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.device_id }}</el-text>
                    <div style="margin: 6px 0" />
                    <el-text class="mx-1" type="success" style="margin-left: 5px;">folder_id:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.folder_id }}</el-text>
                    <div style="margin: 6px 0" />
                    <el-text class="mx-1" type="warning" style="margin-left: 5px;">local_path:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.local_path }}</el-text>
                    <div style="margin: 6px 0" />
                    <el-text class="mx-1" type="danger" style="margin-left: 5px;">last_Sync:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.last_Sync }}</el-text>
                    <div style="margin: 1px 0" />
                    <el-button type="success" style="margin-top: 1%;margin-left:85%;margin-bottom: 1%;" @click="synchronization(remoteFile)">同步</el-button>
                </div>
            </el-scrollbar>
        </div>
    </div>
    <el-drawer
    ref="drawerRef"
    v-model="left_drawer"
    title="远程同步"
    :before-close="handleClose"
    direction="ltr"
    class="demo-drawer"
  >
    <div class="demo-drawer__content">
      <el-form :model="remoteAdd">
        <el-form-item label="device_id:" :label-width="formLabelWidth">
          <el-input v-model="remoteAdd.device_id" autocomplete="off" />
        </el-form-item>
        <el-form-item label="folder_id:" :label-width="formLabelWidth">
          <el-input v-model="remoteAdd.folder_id" autocomplete="off" />
        </el-form-item>
        <el-form-item label="local_path:" :label-width="formLabelWidth">
          <el-input v-model="remoteAdd.local_path" autocomplete="off" />
        </el-form-item>
      </el-form>
      <div class="demo-drawer__footer" >
        <el-button @click="cancelRemote" size="large" style="margin-left: 70%;">取消</el-button>
        <el-button type="primary" :loading="loading" size="large" @click="comfirmRemote">{{
          loading ? 'Submitting ...' : '确认'
        }}</el-button>
      </div>
    </div>
    </el-drawer>
</template>
 
 <script>
    import { defineComponent } from "vue"
	import navigationBar from '../components/header.vue'
    import axios from "axios"

     export default defineComponent({
        name: "index",
         data(){
             return {
                localFiles:[
                {
                    name: 'food.jpeg',
                    url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                {
                    name: 'food2.jpeg',
                    url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                {
                    name: 'food.jpeg',
                    url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                {
                    name: 'food2.jpeg',
                    url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
             
                ],
                remoteFiles:[
                {
                    device_id: 'food.jpeg',
                    folder_id: '1',
                    local_path: 'asdasdasdas',
                    last_Sync: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                {
                    device_id: 'food.jpeg',
                    folder_id: '2',
                    local_path: 'asdasdasdas',
                    last_Sync: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                {
                    device_id: 'food.jpeg',
                    folder_id: '3',
                    local_path: 'asdasdasdas',
                    last_Sync: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                {
                    device_id: 'food.jpeg',
                    folder_id: '4',
                    local_path: 'asdasdasdas',
                    last_Sync: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                ],
                remoteAdd:{
                    device_id: '',
                    folder_id: '',
                    local_path: '',
                },
                autoSync:false,
                timer: "",
                left_drawer:false,
             }
         },
		components: {navigationBar},
         mounted: function () {
             this.ready()
         },
         methods: {
            //在这里面获取Device ID
            ready(){
                let that = this
                let config = {
                    url: "http://localhost:7000/",
                    method: "GET",
                    headers: {},
                };
                axios(config)
                .then(function (response) {
                    console.log(response)
                    that.localFiles.forEach(function (localfile) {   
                        if (localfile.uid === file.file.uid) {
                            localfile.status='success'
                        }
                    });  
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            //本地文件上传
            sendFiles(file){
                let that = this
                let config = {
                    url: "http://localhost:7000/",
                    method: "GET",
                    headers: {},
                };
                axios(config)
                .then(function (response) {
                    console.log(response)
                    that.localFiles.forEach(function (localfile) {   
                        if (localfile.uid === file.file.uid) {
                            localfile.status='success'
                        }
                    });  
                })
                .catch(function (error) {
                    console.log(error);
                });
                
                console.log(file.file.uid)
                console.log(this.localFiles)
            },
            //本地文件删除
            localRemove(uploadFile, uploadFiles){
                console.log(uploadFile, uploadFiles)
                let that = this
                let config = {
                    url: "http://localhost:7000/",
                    method: "GET",
                    headers: {},
                };
                axios(config)
                .then(function (response) {
                    console.log(response)
                    //改变文件状态（右上角）
                    that.localFiles.forEach(function (localfile) {   
                        if (localfile.uid === file.file.uid) {
                            localfile.status='success'
                        }
                    });  
                })
                .catch(function (error) {
                    console.log(error);
                });
                
            },
            localPreview(file){
                console.log(file)
            },
            //删除远程获取的数据
            remoteRemove(remoteFile){
                let that = this;
                that.remoteFiles.splice(that.remoteFiles.indexOf(remoteFile), 1);
                console.log(remoteFile)
            },
            //远程同步文件夹点击填表
            getFiles(){
                let that = this;
                that.left_drawer = true;
            },
            //自动同步
            autoGetFiles(){
                let that = this;
                that.remoteFiles.forEach(function (remoteFile) {   
                    that.synchronization(remoteFile)
                });  
            },
            //自动同步按钮
            Automatic(){
                if(this.autoSync){
                    this.timer = setInterval(this.autoGetFiles, 20000);
                }
                else{
                    clearInterval(this.timer);
                }
            },
            //单击同步按钮
            synchronization(remoteFile){
                let config = {
                    url: "http://localhost:7000/",
                    method: "GET",
                    headers: {},
                };
                axios(config)
                .then(function (response) {
                    console.log(response)
                    
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            //确认获取同步信息
            comfirmRemote(){
                let that = this
                let config = {
                    url: "http://localhost:7000/",
                    method: "GET",
                    headers: {},
                };
                axios(config)
                .then(function (response) {
                    console.log(response)
                    that.localFiles.forEach(function (localfile) {   
                        if (localfile.uid === file.file.uid) {
                            localfile.status='success'
                        }
                    });  
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            //drawer关闭
            cancelRemote(){
                let that = this;
                that.left_drawer = false;
            }
         },
     })
 </script>
 
 <style scoped>
     @import '../assets/css/index.css';
     .scrollbar-demo-item {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 50px;
        margin: 10px;
        text-align: center;
        border-radius: 4px;
        background: var(--el-color-primary-light-9);
        color: var(--el-color-primary);
        }
 </style>
 