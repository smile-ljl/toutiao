<template>
  <div class="comment-container">
    <el-card class="box-card">
      <!-- 面包屑路径导航 -->
      <div slot="header" class="clearfix">
        <el-breadcrumb separator="/">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item><a href="/">评论管理</a></el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <!-- /面包屑路径导航 -->
      <!-- 表格区域 -->
       <el-table
        class="table-list"
        :data="articles"
        stripe
        style="width: 100%">
        <el-table-column
          prop="title"
          label="标题"
        >
        </el-table-column>
        <el-table-column
          prop="total_comment_count"
          label="总评论数"
        >
        </el-table-column>
        <el-table-column
          prop="fans_comment_count"
          label="粉丝评论数"
        >
        </el-table-column>
        <el-table-column
          prop="status"
          label="评论状态"
        >
          <template slot-scope="scope">
            {{ scope.row.comment_status ? '正常' : '关闭'}}
          </template>
        </el-table-column>
        <el-table-column
          prop="operation"
          label="操作"
        >
          <template slot-scope="scope">
            <el-switch
              v-model="scope.row.comment_status"
              active-color="#13ce66"
              inactive-color="#ff4949"
              :disabled="scope.row.statusDisabled"
              @change="onStatusChange(scope.row)">
            </el-switch>
          </template>
        </el-table-column>
      </el-table>
      <!-- /表格区域 -->
      <!-- 分页管理 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="page"
        :page-sizes="[10, 20, 30, 50]"
        :page-size.sync="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="toutalCount"
        background
      >
      </el-pagination>
      <!-- /分页管理结束 -->
  </el-card>
  </div>
</template>
<script>
import {
  getArticles,
  updateCommentStatus
} from '@/api/article'

export default {
  name: 'CommentIndex',
  components: {},
  props: {},
  data () {
    return {
      articles: [], // 文章数据列表
      totalCount: 0,
      pageSize: 10,
      page: 1
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadArticles()
  },
  mounted () {},
  methods: {
    handleSizeChange () {
      this.loadArticles(1)
    },
    handleCurrentChange (page) {
      this.loadArticles(page)
    },
    loadArticles (page = 1) {
      this.page = page
      getArticles({
        response_type: 'comment',
        page,
        per_page: this.pageSize
      }).then(res => {
        // this.articles = res.data.data.results
        const { results } = res.data.data
        results.forEach(article => {
          article.statusDisabled = false
        })
        this.articles = results
        // console.log(this.articles)
        this.totalCount = res.data.data.total_count
      })
    },
    onStatusChange (article) {
      console.log(article)
      // 禁用开关
      article.statusDisabled = true
      // 请求提交修改
      updateCommentStatus(article.id.toString(), article.comment_status).then(res => {
        // 启用开关
        article.statusDisabled = false
        this.$message({
          type: 'success',
          message: article.comment_status ? '开启文章评论状态' : '关闭文章评论状态'
        })
      })
    }
  }
}
</script>

<style scoped lang="less">
.table-list {
  margin-bottom: 30px
}
</style>
