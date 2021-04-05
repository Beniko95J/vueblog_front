<template>
  <div>

    <Header></Header>
    
    <div class="mblog">
      <h2> {{ blog.title}} </h2>
      <div v-if="ownBlog">
        <el-link icon="el-icon-edit">
          <router-link :to="{name: 'BlogEdit', params: {blogId: blog.id}}">
            编辑
          </router-link>
        </el-link>
        <el-divider direction="vertical"></el-divider>
        <el-link icon="el-icon-delete" type="danger" @click="deleteBlog">
          删除
        </el-link>
      </div>
      <el-divider></el-divider>
      <div class="markdown-body" v-html="blog.content"></div>
    </div>

  </div>
</template>

<script>
import Header from "../components/Header";
import "github-markdown-css/github-markdown.css"
export default {
    name: "BlogDetail.vue",
    components: {Header},
    data() {
      return {
        blog: {
          title: "default",
          content: "moren"
        },
        ownBlog: false
      }
    },
    methods: {
      deleteBlog() {
        const blogId = this.$route.params.blogId;
        const _this = this;
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$axios.get('/blog/delete/' + blogId, {
              headers: {
                "Authorization": localStorage.getItem("token")
              }
            }).then(res => {
            console.log(res);
            _this.$alert('操作成功', '提示', {
              confirmButtonText: '确定',
              callback: action => {
                _this.$router.push("/blogs");
              }
            });
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
      }
    },
    created() {
      const blogId = this.$route.params.blogId;
      console.log(blogId);
      const _this = this;
      this.$axios.get('/blog/' + blogId).then(res => {
        const blog = res.data.data;
        _this.blog.id = blog.id;
        _this.blog.title = blog.title;

        var MarkdownIt = require("markdown-it");
        var md = new MarkdownIt();

        var result = md.render(blog.content);

        _this.blog.content = result;

        if (_this.$store.getters.getUser) {
          _this.ownBlog = (blog.userId === _this.$store.getters.getUser.id);
        }
      });
    }
}
</script>

<style>

.mblog {
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  width: 100%;
  min-height: 700px;
  padding: 20px 15px;
}

</style>