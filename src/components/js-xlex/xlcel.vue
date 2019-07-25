<template>
  <span style="margin-right:10px">
    <input
      v-show="false"
      ref="input"
      class="input-file"
      type="file"
      accept=".csv, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel"
      @change="exportData"
    >
    <el-button @click="btnClick">
      上传<i class="el-icon-upload el-icon--right" />
    </el-button>
    <!--div-- id="tableau"></!--div-->
    <el-input v-model="filename" read-only style="width:200px;padding-left:0" />
  </span>

</template>

<script>
import XLSX from 'xlsx'
export default {
  data() {
    return {
      filename: ''
    }
  },
  methods: {
    btnClick() {
      this.$refs.input.value = ''
      document.querySelector('.input-file').click()
    },
    exportData(event) {
      var file = event.target.files
      this.filename = file[0].name
      if (!event.currentTarget.files.length) {
        return
      }
      const that = this
      // 拿取文件对象
      var f = event.currentTarget.files[0]
      // 用FileReader来读取
      var reader = new FileReader()
      // 重写FileReader上的readAsBinaryString方法
      FileReader.prototype.readAsBinaryString = function(f) {
        var binary = ''
        var wb // 读取完成的数据
        var outdata // 你需要的数据
        var reader = new FileReader()
        reader.onload = function(e) {
          // 读取成Uint8Array，再转换为Unicode编码（Unicode占两个字节）
          var bytes = new Uint8Array(reader.result)
          var length = bytes.byteLength
          for (var i = 0; i < length; i++) {
            binary += String.fromCharCode(bytes[i])
          }
          // 接下来就是xlsx了，具体可看api
          wb = XLSX.read(binary, {
            type: 'binary'
          })
          outdata = XLSX.utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]], {
            header: 'A',
            raw: true,
            defval: ' '
          })
          that.$emit('getResult', outdata)
          // 自定义方法向父组件传递数据

          /* var container = document.getElementById('tableau')
          container.innerHTML = XLSX.utils.sheet_to_html(wb.Sheets[wb.SheetNames[0]]) */
        }
        reader.readAsArrayBuffer(f)
      }
      reader.readAsBinaryString(f)
    }
  }
}
</script>

<style>
</style>
！
