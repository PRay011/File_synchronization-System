<template>
  <navigationBar></navigationBar>
  <div class="container">
    <div class="local">
      <el-text class="mx-1" size="large" style="margin-left: 0%;" type="primary">本地共享文件夹：(以 / 结尾)</el-text>
      <div style="margin: 7px 0"/>
      <el-input v-model="localFolderPath" autocomplete="off" style="width: 400px"/>
      <el-button type="primary" @click="addLocalFile">Click to add</el-button>
      <el-scrollbar max-height="600px" style="margin-top: 10px;">
        <div v-for="localFile in localFiles" :key="localFile.folderId" class="localFile">
          <el-icon style="position: relative; margin-left:96%;">
            <Close @click="localRemove(localFile)"/>
          </el-icon>
          <div style="margin: 1px 0"/>
          <el-text class="mx-1" style="margin-left: 5px;" type="primary">folder id:</el-text>
          <el-text class="mx-1" style="margin-left: 10px;">{{ localFile.folderId }}</el-text>
          <div style="margin: 6px 0"/>
          <el-text class="mx-1" style="margin-left: 5px;" type="success">folder path:</el-text>
          <el-text class="mx-1" style="margin-left: 10px;">{{ localFile.folderPath }}</el-text>
          <div style="margin: 1px 0"/>
        </div>
      </el-scrollbar>
    </div>
    <div class="remote">
      <el-text class="mx-1" size="large" style="margin-left: 0%;" type="primary">远程同步文件夹：</el-text>
      <div style="margin: 7px 0"/>
      <el-button type="primary" @click="getFiles">Click to add</el-button>
      <el-text class="mx-1" style="margin-left: 37%;" type="primary">Automatic synchronization:</el-text>
      <el-switch v-model="autoSync" style="margin-left: 1%;" @change="Automatic"/>
      <el-scrollbar max-height="600px" style="margin-top: 2px;">
        <div v-for="remoteFile in remoteFiles" :key="remoteFile.device_id" class="files">
          <el-icon style="position: relative; margin-left:96%;">
            <Close @click="remoteRemove(remoteFile)"/>
          </el-icon>
          <div style="margin: 1px 0"/>
          <el-text class="mx-1" style="margin-left: 5px;" type="primary">device id:</el-text>
          <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.deviceId }}</el-text>
          <div style="margin: 6px 0"/>
          <el-text class="mx-1" style="margin-left: 5px;" type="success">folder id:</el-text>
          <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.folderId }}</el-text>
          <div style="margin: 6px 0"/>
          <el-text class="mx-1" style="margin-left: 5px;" type="warning">local path:</el-text>
          <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.localPath }}</el-text>
          <div style="margin: 6px 0"/>
          <el-text class="mx-1" style="margin-left: 5px;" type="danger">last sync time:</el-text>
          <el-text class="mx-1" style="margin-left: 10px;">{{ remoteFile.lastSync }}</el-text>
          <div style="margin: 1px 0"/>
          <el-button style="margin-top: 1%;margin-left:85%;margin-bottom: 1%;" type="success"
                     @click="synchronization(remoteFile)">同步
          </el-button>
        </div>
      </el-scrollbar>
    </div>
  </div>
  <el-drawer
      ref="drawerRef"
      v-model="left_drawer"
      :before-close="handleClose"
      class="demo-drawer"
      direction="ltr"
      title="远程同步"
  >
    <div class="demo-drawer__content">
      <el-form :model="remoteAdd">
        <el-form-item :label-width="formLabelWidth" label="device_id:">
          <el-input v-model="remoteAdd.device_id" autocomplete="off"/>
        </el-form-item>
        <el-form-item :label-width="formLabelWidth" label="folder_id:">
          <el-input v-model="remoteAdd.folder_id" autocomplete="off"/>
        </el-form-item>
        <el-form-item :label-width="formLabelWidth" label="local_path: (以 / 结尾)">
          <el-input v-model="remoteAdd.local_path" autocomplete="off"/>
        </el-form-item>
      </el-form>
      <div class="demo-drawer__footer">
        <el-button size="large" style="margin-left: 70%;" @click="cancelRemote">取消</el-button>
        <el-button :loading="loading" size="large" type="primary" @click="comfirmRemote">{{
            loading ? 'Submitting ...' : '确认'
          }}
        </el-button>
      </div>
    </div>
  </el-drawer>
</template>

<script>
import {defineComponent} from "vue"
import navigationBar from '../components/header.vue'
import axios from "axios"

export default defineComponent({
  name: "index",
  data() {
    return {
      localFiles: [],
      localFolderPath: "",
      remoteFiles: [],
      remoteAdd: {
        device_id: '',
        folder_id: '',
        local_path: '',
      },
      autoSync: false,
      timer: "",
      left_drawer: false,
      folderPath: ''
    }
  },
  components: {navigationBar},
  mounted: function () {
    this.getLocalFolders();
    this.getRemoteFolders();
  },
  methods: {
    // TODO 加载数据
    getLocalFolders() {
      let that = this
      let config = {
        url: "/api/localFolder",
        method: "GET",
        headers: {},
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            that.localFiles = res.data;
            console.log(res)
          })
          .catch(function (error) {
            console.log(error);
          });
    },
    getRemoteFolders() {
      let that = this
      let config = {
        url: "/api/remoteFolder",
        method: "GET",
        headers: {},
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            that.remoteFiles = res.data;
            console.log(res)
          })
          .catch(function (error) {
            console.log(error);
          });
    },

    //本地文件上传
    addLocalFile(file) {
      let that = this
      let config = {
        url: "/api//localFolder?folderPath=" + encodeURIComponent(that.localFolderPath),
        method: "POST",
        headers: {}
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            console.log(res)
            if (res.status === 200) {
              that.getLocalFolders()
            }
          })
          .catch(function (error) {
            console.log(error);
          });

      console.log(file.file)
      console.log(this.localFiles)
    },
    //本地文件删除
    localRemove(localFile) {
      let that = this
      let config = {
        url: "/api//localFolder?folderId=" + localFile.folderId,
        method: "DELETE",
        headers: {},
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            alert(res.data);
            if (res.status === 200) {
              that.getLocalFolders()
            }
          })
          .catch(function (error) {
            console.log(error);
          });
    },

    //远程同步文件夹点击填表
    getFiles() {
      let that = this;
      that.left_drawer = true;
    },
    //添加远程同步文件夹
    comfirmRemote() {
      let that = this
      let config = {
        url: "/api/remoteFolder",
        method: "POST",
        headers: {},
        data: {
          deviceId: that.remoteAdd.device_id,
          folderId: that.remoteAdd.folder_id,
          localPath: that.remoteAdd.local_path,
          lastSync: "未同步"
        }
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            alert(res.data)
            if (res.status === 200) {
              that.getRemoteFolders()
            }
          })
          .catch(function (error) {
            console.log(error);
          });
    },
    //drawer关闭
    cancelRemote() {
      let that = this;
      that.left_drawer = false;
    },

    //删除远程文件夹
    remoteRemove(remoteFile) {
      let that = this
      let config = {
        url: "/api/remoteFolder?folderId=" + remoteFile.folderId,
        method: "DELETE",
        headers: {},
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            alert(res.data);
            if (res.status === 200) {
              that.getRemoteFolders()
            }
          })
          .catch(function (error) {
            console.log(error);
          });
    },

    //单击同步按钮
    synchronization(remoteFile) {
      let that = this;
      let config = {
        url: "/api/remoteFolder?folderId=" + remoteFile.folderId,
        method: "PUT",
        headers: {},
      };
      axios(config)
          .then(function (response) {
            let res = response.data;
            if (res.status === 200) {
              that.getRemoteFolders()
            }
          })
          .catch(function (error) {
            console.log(error);
          });
    },

    //自动同步按钮
    Automatic() {
      if (this.autoSync) {
        this.timer = setInterval(this.autoGetFiles, 5000);
      } else {
        clearInterval(this.timer);
      }
    },


    //自动同步
    autoGetFiles() {
      let that = this;
      that.remoteFiles.forEach(function (remoteFile) {
        that.synchronization(remoteFile)
      });
    },


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
 