<template>
  <div>
    <div class="bg"></div>
    <Header></Header>
    <div class="block">
      <el-timeline class="m-timeline">
        <el-timeline-item :timestamp="blog.created" placement="top" v-for="blog in blogs" :key="blog.id">
          <el-card class="m-card">
            <h4>
              <router-link :to="{name: 'BlogDetail', params: {blogId: blog.id}}">
                {{ blog.title }}
              </router-link>
            </h4>
            <p>{{ blog.description }}</p>
          </el-card>
        </el-timeline-item>
      </el-timeline>
      <div class="m-page">
        <el-pagination
          background
          layout="prev, pager, next"
          :current-page = "currentPage"
          :page-size = "pageSize"
          :total="total"
          @current-change=page>
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "../components/Header";
export default {
  name: "Blogs.vue",
  components: {Header},
  data() {
    return {
      blogs: {},
      currentPage: 1,
      total: 0,
      pageSize: 5
    }
  },
  methods: {
    page (currentPage) {
      const _this = this;
      _this.$axios.get("/blogs?currentPage=" + currentPage).then(res => {
        _this.blogs = res.data.data.records;
        _this.currentPage = res.data.data.current;
        _this.total = res.data.data.total;
        _this.pageSize = res.data.data.size;
      })
    }
  },
  created() {
    this.page(1);
  }
}
</script>

<style>
.m-page {
  margin: 0 auto;
  padding-bottom: 16px;
  text-align: center;
}

.m-timeline {
  max-width: 900px;
  margin: 0 auto !important;
  padding-inline-start: 0px;
}

.bg {
  background-image: url('../assets/banner/sekiro.jpg');
  background-position: center;
  background-size: cover;
  height: 100%;
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  filter: blur(3px);
  z-index: -1;
}

.m-card {
  height: 96px;
}
</style>
