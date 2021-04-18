<template>
  <div>

    <el-container>
      <el-main>
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
          <el-form-item label="用户名" prop="username">
            <el-input v-model="ruleForm.username"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input type="password" v-model="ruleForm.password" @keyup.enter.native="submitForm('ruleForm')"></el-input>
          </el-form-item>

          <el-form-item class="m-ruleForm-button">
            <el-button type="primary" @click="submitForm('ruleForm')">登录</el-button>
            <el-button @click="resetForm('ruleForm')">重置</el-button>
          </el-form-item>
        </el-form>

      </el-main>
    </el-container>

  </div>
</template>

<script>
  export default {
    name: "Login",
    data() {
      return {
        ruleForm: {
          username: '',
          password: ''
        },
        rules: {
          username: [
            { required: true, message: '请输入用户名', trigger: 'blur' },
            { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '请选择密码', trigger: 'change' }
          ]
        }
      };
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            const _this = this
            this.$axios.post('/login', this.ruleForm).then(res => {
              const jwt = res.headers['authorization']
              const userInfo = res.data.data
              // 把数据共享出去
              _this.$store.commit("SET_TOKEN", jwt)
              _this.$store.commit("SET_USERINFO", userInfo)
              // 获取
              _this.$router.push("/blogs")
            })
          } else {
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      }
    }
  }
</script>

<style scoped>
  .el-main {
    background-color: #E9EEF3;
    padding: 40px;
  }

  .demo-ruleForm {
    max-width: 500px;
    margin: 0 auto;
  }

  .m-ruleForm-button {
    text-align: center;
    margin-bottom: 0px;
  }
</style>