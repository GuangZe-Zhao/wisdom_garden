<template>
  <div class="login_body">
    <div class="bg" />
    <div class="box">
      <div class="title">智慧园区-登录</div>
      <!--
        基础校验
          el-form  :model="表单对象" :rules="规则对象"
          el-form-item  prop属性指定一下要使用哪条规则
          el-input  v-model双向绑定

        统一校验
          1. 获取表单的实例对象
          2. 调用validate方法
      -->
      <el-form ref="form" :model="form" :rules="rules">
        <el-form-item
          label="账号"
          prop="username"
        >
          <el-input v-model="form.username" />
        </el-form-item>

        <el-form-item
          label="密码"
          prop="password"
        >
          <el-input v-model="form.password" />
        </el-form-item>
        <!--
          1. 完成选择框的双向绑定 得到一个true或者false的选中状态
          2. 如果当前为true，点击登录时，表示要记住，把当前的用户名和密码存入本地
          3. 组件初始化的时候，从本地取账号和密码，把账号密码存入用来双向绑定的form身上
          4. 如果当前用户没有记住，状态为false，点击登录的时候要把之前的数据清空
        -->
        <el-form-item prop="remember">
          <el-checkbox v-model="remember">记住我</el-checkbox>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" class="login_btn" @click="loginHandler">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
const REMEMBER_KEY = 'remember_key'
export default {
  name: 'Login',
  data() {
    return {
      // 表单对象
      form: {
        username: '',
        password: ''
      },
      remember: false,
      // 规则对象
      rules: {
        username: [
          {
            required: true,
            message: '请输入账号',
            trigger: 'blur'
          }
        ],
        password: [
          {
            required: true,
            message: '请输入密码',
            trigger: 'blur'
          }
        ]
      }
    }
  },
  created() {
    // 去本地取一下之前存入的账号和密码 如果取到了 赋值操作
    const formStr = localStorage.getItem(REMEMBER_KEY)
    if (formStr) {
      const formObj = JSON.parse(formStr)
      this.form = formObj
    }
  },
  methods: {
    loginHandler() {
      this.$refs.form.validate(async valid => {
        // 所有的表单项都通过校验 valid变量才为true 否则就是false
        if (valid) {
          // 添加记住我逻辑
          if (this.remember) {
            localStorage.setItem(REMEMBER_KEY, JSON.stringify(this.form))
          } else {
            localStorage.removeItem(REMEMBER_KEY)
          }
          // TODO LOGIN
          // 这里确保token返回之后再跳转到首页 防止首页有一些需要依赖token的逻辑
          await this.$store.dispatch('user/asyncLogin', this.form)
          // 跳转到首页
          this.$router.push('/')
          // 提示用户登录成功
          this.$message({
            type: 'success',
            message: '登录成功'
          })
        }
      })
    }
  }
}

</script>

<style scoped lang="scss">
  .login_body {
    display: flex;
  }
  .bg {
    width: 60vw;
    height: 100vh;
    background: url('~@/assets/login-bg.svg') no-repeat;
    background-position: right top;
    background-size: cover;
  }
  .box {
    margin: 200px 10% 0;
    flex: 1;
    .title {
      padding-bottom: 76px;
      font-size: 26px;
      font-weight: 500;
      color: #1e2023;
    }
    ::v-deep() {
      .ant-form-item {
        display: flex;
        margin-bottom: 62px;
        flex-direction: column;
      }
      .ant-form-item-label label {
        font-size: 16px;
        color: #8b929d;
      }
      .ant-input,
      .ant-input-password {
        border-radius: 8px;
      }
    }
  }
  .login_btn{
    width: 100%;
  }
</style>
