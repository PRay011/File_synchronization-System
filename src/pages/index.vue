<template>
    <navigationBar></navigationBar>
    <div class="container">
        <div class="local">
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
            <el-button type="primary">Click to upload</el-button>    
            </el-upload>
            </el-scrollbar>
        </div>
        <div class="remote">
            <el-scrollbar max-height="600px" style="margin-top: 10px;">  
            <el-button type="primary" @click="getFiles">Click to upload</el-button>    
                <div class="files" v-for="remoteFile in remoteFiles" :key="remoteFile.device_id">
                    <el-icon style="position: relative; margin-left:96%;"><Close @click="remoteRemove(remoteFile)" /></el-icon>
                    <div style="margin: 1px 0" />
                    <el-text class="mx-1" type="primary" style="margin-left: 5px;">device_id:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.device_id }}</el-text>
                    <div style="margin: 8px 0" />
                    <el-text class="mx-1" type="success" style="margin-left: 5px;">folder_id:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.folder_id }}</el-text>
                    <div style="margin: 8px 0" />
                    <el-text class="mx-1" type="warning" style="margin-left: 5px;">local_path:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.local_path }}</el-text>
                    <div style="margin: 8px 0" />
                    <el-text class="mx-1" type="danger" style="margin-left: 5px;">last_Sync:</el-text>
                    <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.last_Sync }}</el-text>
                </div>
            </el-scrollbar>
        </div>
    </div>
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
                    folder_id: '1231231',
                    local_path: 'asdasdasdas',
                    last_Sync: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                {
                    device_id: 'food.jpeg',
                    folder_id: '1231231',
                    local_path: 'asdasdasdas',
                    last_Sync: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100',
                },
                ]

             }
         },
		components: {navigationBar},
         mounted: function () {
             this.ready()
         },
         methods: {
            ready(){

            },
            sendFiles(file){
                let that = this;
                that.localFiles.forEach(function (localfile) {   
                if (localfile.uid === file.file.uid) {
                    localfile.status='success'
                }
                });
                console.log(file.file.uid)
                console.log(this.localFiles)
            },
            localRemove(uploadFile, uploadFiles){
                console.log(uploadFile, uploadFiles)
                // console.log(this.localFiles)
            },
            localPreview(file){
                console.log(file)
            },
            remoteRemove(remoteFile){
                console.log(remoteFile)
                // console.log(this.localFiles)
            },
            getFiles(){

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
 