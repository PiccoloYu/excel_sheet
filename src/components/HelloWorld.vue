<template>
  <div id="app">
    <div class="tool">
      <el-button type="primary" @click="getSourceData()">获取选中区域数据</el-button>
      <el-button @click="getexcel()">导出csv</el-button>
      <el-button @click="getxlsx()">导出xlsx</el-button>
      <el-button @click="clear()">清空表格</el-button>
      <inputExcel class="inputExcel" @getResult="getMyExcelData" />
    </div>
    <hot-table
      :id="id"
      ref="hotTableComponent"
      :settings="hotSettings"
      :style="style"
      :language="language"
      :class-name="className"
      license-key="non-commercial-and-evaluation"
    />
  </div>
</template>

<script>
import Handsontable from 'handsontable'
import { HotTable } from '@handsontable/vue'
import inputExcel from '@/components/js-xlex/xlcel'
import 'handsontable/languages/zh-CN'
import 'handsontable/dist/handsontable.full.css'
import XLSX from 'xlsx'
export default {
  name: 'DB',
  components: {
    HotTable,
    inputExcel
  },
  data() {
    return {
      language: 'zh-CN',
      hotSettings: {
        data: [[]],
        minRows: 100,
        minCols: 100,
        colHeaders: true,
        rowHeaders: true,
        contextMenu: true,
        dropdownMenu: false,
        headerTooltips: true, // 工具栏启用
        mergeCells: true,
        manualColumnResize: true, // 列拖拽改变大小
        manualRowResize: true, // 行拖拽改变大小
        outsideClickDeselects: false,
        search: true, // 查询
        stretchH: 'all'
      },
      id: 'my-custom-id',
      className: 'my-custom-classname',
      style: 'overflow: hidden;'
    }
  },
  computed: {
    // 计算属性 computed只在初始化时被调用 computed用来监控自己定义的变量，该变量不在data里面声明，直接在computed里面定义，然后就可以在页面上进行双向数据绑定展示出结果或者用作其他处理；
    /* rowSelection () {
      const { selectedRowKeys } = this// 该语句等价于：const a = b.a es6解构赋值
      return {selectedRowKeys,
        onChange: this.onSelectChange // 方法onSelectChange
      }
    } */
  },
  watch: {},
  created() {
    // 在模板渲染成html前调用，即通常初始化某些属性值 然后再渲染成视图。
  },
  mounted() {},
  methods: {
    // methods会在数据变化时被调用, 即使变动的数据与自身无关
    /* onSelectChange (selectedRowKeys) {
      // console.log('selectedRowKeys changed: ', selectedRowKeys)
      this.selectedRowKeys = selectedRowKeys
    } */
    getdate() {
      const date = new Date()
      date.setTime(date.getTime())
      const year = date.getFullYear()
      const month = date.getMonth() + 1
      const day = date.getDate()
      const hour = date.getHours() // 得到小时
      const minu = date.getMinutes() // 得到分钟
      const sec = date.getSeconds() // 得到秒
      const now =
        year + '-' + month + '-' + day + ' ' + hour + ':' + minu + ':' + sec
      return now
    },
    clear() {
      this.getdate()
      this.hotSettings.data = [[]]
    },
    getxlsx() {
      /* let table = document.querySelector('.htCore')
      let sheet = XLSX.utils.table_to_sheet(table)// 将一个table对象转换成一个sheet对象
      console.log(sheet)
      /* openDownloadDialog(sheet2blob(sheet), filename + '.xlsx') */
      const data = this.$refs.hotTableComponent.hotInstance.getData()
      const ws = XLSX.utils.aoa_to_sheet(data)
      const wb = XLSX.utils.book_new()
      XLSX.utils.book_append_sheet(wb, ws, 'SheetJS')
      const name = this.getdate()
      /* generate file and send to client */
      XLSX.writeFile(wb, `${name}.xlsx`)
    },
    getexcel() {
      var exportPlugin1 = this.$refs.hotTableComponent.hotInstance.getPlugin(
        'exportFile'
      )
      exportPlugin1.downloadFile('csv', {
        bom: false,
        columnDelimiter: ',',
        columnHeaders: false,
        exportHiddenColumns: true,
        exportHiddenRows: true,
        fileExtension: 'csv',
        filename: 'CSV-file_[YYYY]-[MM]-[DD]',
        mimeType: 'text/csv',
        rowDelimiter: '\r\n',
        rowHeaders: false
      })
    },
    getSourceData() {
      const dat2 = this.$refs.hotTableComponent.hotInstance.getSelectedLast()
      console.log(dat2)
      const row = dat2[0]
      const column = dat2[1]
      const row2 = dat2[2]
      const column2 = dat2[3]
      const data1 = this.$refs.hotTableComponent.hotInstance.getSourceData(row, column, row2, column2)
      console.log(data1)
    },
    getAllLanguageOptions: function() {
      const allDictionaries = Handsontable.languages.getLanguagesDictionaries()
      for (const language of allDictionaries) {
        console.log(language.languageCode)
      }
    },
    getMyExcelData(data) {
      // data 为读取的excel数据，在这里进行处理该数据
      if (data.length === 0 || data === '') {
        this.$notification.open({
          message: '错误！！',
          description: '请不要上传空白文件',
          icon: <a-icon type='warning' style='color: red' />
        })
      } else {
        if (data.length) {
          this.hotSettings.colHeaders = Object.keys(data[0])
        } else {
          this.hotSettings.colHeaders = []
        }

        const data1 = []
        for (let i = 0; i < data.length; i++) {
          const data2 = []
          for (var key in data[i]) {
            data2.push(data[i][key])
          }
          data1.push(data2)
        }

        this.hotSettings.data = data1
      }
    },
    reversal() {},
    arraydata(array) {
      var ans = []
      array.forEach((item, i) => {
        // data筛选
        ans[i] = {}
        ans[i].total = item.length
        ans[i].array = item.filter(n => n)
      })

      const tmp = []
      for (let i = 0; i < ans.length; i++) {
        if (ans[i].array.length !== 0) {
          tmp.push(ans[i])
        }
      }
      return tmp
    }
  }
}
</script>

<style  lang="less"  scoped>
#app {
  width: 82vw;
  height: 70vh;
}
.my-custom-classname {
  width: 100%;
  height: 100%;
}
.tool {
  text-align: left;
  padding-bottom: 5px;
}
.inputExcel {
  margin-left: 10px;
}
</style>
