<template>
  <div>
    <Header></Header>
    <div class="m-content">
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
        <el-form-item label="标题" prop="title">
          <el-input v-model="ruleForm.title"></el-input>
        </el-form-item>
        
        <el-form-item label="摘要" prop="description">
          <el-input type="textarea" v-model="ruleForm.description"></el-input>
        </el-form-item>

        <el-form-item label="内容" prop="content">
          <mavon-editor v-model="ruleForm.content"
                        ref="md"
                        @imgAdd="handleImgAdd"
                        @imgDel="handleImgDel">
          </mavon-editor>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">修改</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import Header from "../components/Header";
export default {
  name: "BlogEdit.vue",
  components: {Header},
  data() {
    return {
      ruleForm: {
        id: '',
        title: '',
        description: '',
        content: ''
      },
      rules: {
        title: [
          { required: true, message: '请输入标题', trigger: 'blur' },
          { min: 2, max: 50, message: '长度在 2 到 50 个字符', trigger: 'blur' }
        ],
        description: [
          { required: true, message: '请输入摘要', trigger: 'blur' }
        ],
        content: [
          { required: true, message: '请选择内容', trigger: 'blur' }
        ]
      }
    };
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          const _this = this;
          this.$axios.post('/blog/edit', this.ruleForm, {
            headers: {
              "Authorization": localStorage.getItem("token")
            }
          }).then(res => {
            _this.$alert('操作成功', '提示', {
              confirmButtonText: '确定',
              callback: action => {
                _this.$router.push("/blogs");
              }
            });
          });
        } else {
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    handleImgAdd(pos, file) {
      console.log(pos);
      console.log(file);
      console.log(file.size);

      const formData = new FormData();
      formData.append('file', file);
      this.$axios.post('/upload', formData, {
        headers: {
          "Authorization": localStorage.getItem("token")
        }
      }).then(res => {
        console.log(res);
        const url = res.data.data;
        this.$refs.md.$img2Url(pos, url);
      })
    },
    handleImgDel(pos) {
      console.log(pos[0]);
    }
  },
  created() {
    const blogId = this.$route.params.blogId;
    const _this = this;
    if (blogId) {
      this.$axios.get('/blog/' + blogId).then(res => {
        const blog = res.data.data;
        _this.ruleForm.id = blog.id;
        _this.ruleForm.title = blog.title;
        _this.ruleForm.description = blog.description;
        _this.ruleForm.content = blog.content;
      });
    }
  }
}
</script>

<style>
.m-content {
  max-width: 1200px;
  margin: 0 auto;
  text-align: center;
}
</style>
