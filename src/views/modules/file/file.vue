<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @submit.native.prevent>
      <el-form-item style="display: inline-block">
        <el-select v-model="dataForm.module" clearable>
          <el-option v-for="module in moduleList" :key="module.parKey" :value="module.parKey" :label="module.parValue"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查看</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      height="800"
      style="width: 100%;">
      <el-table-column
        fixed="left"
        prop="id"
        header-align="center"
        align="center"
        width="70px"
        label="id">
      </el-table-column>
      <el-table-column
        prop="module"
        header-align="center"
        align="center"
        width="100px"
        label="模块">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.module === 0" size="small" type="success">文章</el-tag>
          <el-tag v-if="scope.row.module === 1" size="small" type="warning">视频</el-tag>
          <el-tag v-if="scope.row.module === 2" size="small" type="error">友链</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="fileName"
        header-align="center"
        align="center"
        treeKey="id"
        width="200px"
        :show-overflow-tooltip="true"
        label="文件名称">
      </el-table-column>
      <el-table-column
        prop="url"
        header-align="center"
        align="center"
        width="200px"
        :show-overflow-tooltip="true"
        label="url地址">
      </el-table-column>
      <el-table-column
        prop="storageType"
        header-align="center"
        align="center"
        width="150px"
        :show-overflow-tooltip="true"
        label="存储类型">
      </el-table-column>
      <el-table-column
        prop="bucketName"
        header-align="center"
        align="center"
        width="150px"
        :show-overflow-tooltip="true"
        label="桶名">
      </el-table-column>
      <el-table-column
        prop="fileMd5"
        header-align="center"
        align="center"
        width="150px"
        :show-overflow-tooltip="true"
        label="文件的md5">
      </el-table-column>
      <el-table-column
        prop="fileSize"
        header-align="center"
        align="center"
        width="100px"
        :show-overflow-tooltip="true"
        label="文件大小">
      </el-table-column>
      <el-table-column
        prop="suffix"
        header-align="center"
        align="center"
        width="100px"
        :show-overflow-tooltip="true"
        label="文件格式">
      </el-table-column>
      <el-table-column
        prop="isChunk"
        header-align="center"
        align="center"
        width="100px"
        :show-overflow-tooltip="true"
        label="是否分片">
      </el-table-column>
      <el-table-column
        prop="chunkCount"
        header-align="center"
        align="center"
        width="100px"
        :show-overflow-tooltip="true"
        label="分片总数量">
      </el-table-column>
      <el-table-column
        prop="uploadStatus"
        header-align="center"
        align="center"
        width="100px"
        :show-overflow-tooltip="true"
        label="上传状态">
      </el-table-column>
      <el-table-column
        prop="updateTime"
        header-align="center"
        align="center"
        width="180px"
        label="更新时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        prop="createTime"
        header-align="center"
        align="center"
        min-width="180px"
        label="创建时间">
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
  </div>
</template>

<script>
export default {
  data () {
    return {
      moduleList: this.getSysParamArr('MODULE_TYPE'),
      dataForm: {
        module: ''
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 20,
      totalPage: 0,
      dataListLoading: false
    }
  },
  activated () {
    this.getDataList()
  },
  beforeDestroy () {
    // 移除监听器
    document.removeEventListener('keydown', this.keyDown)
  },
  mounted () {
    // 监听回车键
    document.addEventListener('keydown', this.keyDown)
  },
  methods: {
    keyDown () {
      // 13代表回车键
      if (window.event.keyCode === 13) {
        this.getDataList()
      }
    },
    // 每页数
    sizeChangeHandle (val) {
      this.pageSize = val
      this.pageIndex = 1
      this.getDataList()
    },
    // 当前页
    currentChangeHandle (val) {
      this.pageIndex = val
      this.getDataList()
    },
    // 获取数据列表
    getDataList () {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/manage/file/resource/list'),
        method: 'get',
        params: this.$http.adornParams({
          'page': this.pageIndex,
          'limit': this.pageSize,
          'module': this.dataForm.module
        })
      }).then((response) => {
        if (response && response.code === 200) {
          this.dataList = response.data.list
          this.totalPage = response.data.totalCount
        } else {
          this.dataList = []
          this.totalPage = 0
        }
        this.dataListLoading = false
      })
    }
  }
}
</script>
