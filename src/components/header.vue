<template>
    <div class="container">
        <el-text class="mx-1" size="large">File Synchronization System</el-text>
        <el-text class="mx-1" size="large" style="margin-left: 50%;">Your Device ID:</el-text>
        <el-text class="mx-1" size="large" style="margin-left: 1%;">xxxxxxxx-xxxx- xxxx-xxxxxxxxxxxxxxxx</el-text>
    </div>
    <el-divider />
 </template>
 
 <script>
     import axios from "axios"
 
     export default {
         data(){
             return {
                 data:''
             }
         },
         mounted: function () {
             this.ready()
         },
         methods: {
             ready(){
                 
             }
         },
     }
 </script>
 
 <style scoped>
    @import '../assets/css/header.css';
 </style>
 