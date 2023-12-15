<template>
  <div class="component-upload-image">
    <el-upload
      :action="uploadImgUrl"
      list-type="picture-card"
      :on-success="handleUploadSuccess"
      :before-upload="handleBeforeUpload"
      :limit="limit"
      :on-error="handleUploadError"
      :on-exceed="handleExceed"
      name="file"
      :on-remove="handleRemove"
      :show-file-list="true"
      :headers="headers"
      :http-request="httpRequest"
      :file-list="fileList"
      :disabled="disabled"
      accept=".jpeg,.jpg,.png"
      :on-preview="handlePictureCardPreview"
      :class="{hide: this.fileList.length >= this.limit}"
    >
      <i class="el-icon-plus" />
    </el-upload>

    <!-- 上传提示 -->
    <div v-if="showTip" slot="tip" class="el-upload__tip">
      请上传
      <template v-if="fileSize"> 大小不超过 <b style="color: #f56c6c">{{ fileSize }}MB</b> </template>
      <template v-if="fileType"> 格式为 <b style="color: #f56c6c">{{ fileType.join("/") }}</b> </template>
      的文件
    </div>

    <el-dialog
      :visible.sync="dialogVisible"
      title="预览"
      width="800"
      append-to-body
    >
      <img
        :src="dialogImageUrl"
        style="display: block; max-width: 100%; margin: 0 auto"
      >
    </el-dialog>
  </div>
</template>

<script>
// import authToken from '../../util/auth'
// import { mapState } from 'vuex'
// import axios from 'axios'
import { uploadFile } from '@/api/newProjectapi/uploadImage'
import { deletePic } from '@/api/userstation'

export default {
  props: {
    // 图片回显
    value: [String, Object, Array],
    // 图片数量限制
    limit: {
      type: Number,
      default: 3
    },
    fileTypeNum: {
      type: String,
      default: ''
    },
    fileLableNum: {
      type: String,
      default: ''
    },
    // 大小限制(MB)
    fileSize: {
      type: Number,
      default: 5
    },
    // 文件类型, 例如['png', 'jpg', 'jpeg']
    fileType: {
      type: Array,
      default: () => ['png', 'jpg', 'jpeg']
    },
    // 是否显示提示
    isShowTip: {
      type: Boolean,
      default: true
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      dialogImageUrl: '',
      dialogVisible: false,
      hideUpload: false,
      uploadImgUrl: 'action', // 上传的图片服务器地址
      headers: {
        'Content-Type': 'multipart/form-data'
      },
      fileList: []
    }
  },
  computed: {
    // 是否显示提示
    showTip() {
      return this.isShowTip && (this.fileType || this.fileSize)
    }

  },
  watch: {
    value: {
      handler(val) {
        if (val) {
          // 首先将值转为数组
          const list = Array.isArray(val) ? val : this.value.split(',')
          // 然后将数组转为对象数组
          var s = {}
          this.fileList = list.map(item => {
            s = { name: item.fileName, url: '/filepath' + item.filePath }
            return s
          })
        } else {
          this.fileList = []
          return []
        }
      },
      deep: true,
      immediate: true
    }
  },
  created() {
  },
  methods: {
    // 删除图片
    handleRemove(file, fileList) {
      const findex = this.fileList.map(f => f.name).indexOf(file.name)
      if (findex > -1) {
        this.fileList.splice(findex, 1)
        this.$emit('input', this.listToString(this.fileList))
      }
      var id = ''
      this.value.forEach(element => {
        if ('/filepath' + element.filePath === file.url) {
          id = element.fileId
        }
      })
      deletePic(id).then(res => {
        this.$message({
          message: '删除成功！',
          type: 'success'
        })
      })
    },
    // 上传成功回调
    handleUploadSuccess(res) {
      this.$message.success('上传成功')
      this.loading.close()
    },
    // 上传前loading加载
    handleBeforeUpload(file) {
      let isImg = false
      if (this.fileType.length) {
        let fileExtension = ''
        if (file.name.lastIndexOf('.') > -1) {
          fileExtension = file.name.slice(file.name.lastIndexOf('.') + 1)
        }
        isImg = this.fileType.some((type) => {
          if (file.type.indexOf(type) > -1) return true
          if (fileExtension && fileExtension.indexOf(type) > -1) return true
          return false
        })
      } else {
        isImg = file.type.indexOf('image') > -1
      }

      if (!isImg) {
        this.$message.error(
          `文件格式不正确, 请上传${this.fileType.join('/')}图片格式文件!`
        )
        return false
      }
      if (this.fileSize) {
        const isLt = file.size / 1024 / 1024 < this.fileSize
        if (!isLt) {
          this.$message.error(`上传头像图片大小不能超过 ${this.fileSize} MB!`)
          return false
        }
      }
      this.loading = this.$loading({
        lock: true,
        text: '上传中',
        background: 'rgba(0, 0, 0, 0.7)'
      })
    },
    // 文件个数超出
    handleExceed() {
      this.$message.error(`上传文件数量不能超过 ${this.limit} 个!`)
    },
    // 上传失败
    handleUploadError(res) {
      this.$message({
        type: 'error',
        message: '上传失败'
      })
      this.loading.close()
    },
    // 预览
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url
      this.dialogVisible = true
    },
    // 对象转成指定字符串分隔
    listToString(list, separator) {
      let strs = ''
      separator = separator || ','
      for (const i in list) {
        strs += list[i].url + separator
      }
      return strs !== '' ? strs.substr(0, strs.length - 1) : ''
    },
    httpRequest(param) {
      const formData = new FormData()
      formData.append('file', param.file)
      formData.append('type', this.fileTypeNum)
      formData.append('lable', this.fileLableNum)
      uploadFile(formData).then(res => {
        if (res.code === 20000) {
          this.$emit('imagesArr', res.data[0].fileId)
          this.$message.success('上传成功')
          this.loading.close()
        }
      })
    }
  }
}
</script>
<style scoped lang="scss">
// .el-upload--picture-card 控制加号部分
::v-deep.hide .el-upload--picture-card {
  display: none;
}
// 去掉动画效果
::v-deep .el-list-enter-active,
::v-deep .el-list-leave-active {
  transition: all 0s;
}

::v-deep .el-list-enter, .el-list-leave-active {
  opacity: 0;
  transform: translateY(0);
}
>>>.el-upload-list--picture-card .el-upload-list__item {
  width: 100px;
  height: 100px;
}
>>>.el-upload--picture-card {
  width: 100px;
  height: 100px;
  line-height: 100px;
}
</style>
