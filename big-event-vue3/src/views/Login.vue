<script setup>
import {User, Lock} from '@element-plus/icons-vue'
import {ref, reactive} from 'vue'
import {useRouter} from 'vue-router'
import {ElMessage} from 'element-plus'
import {loginService, registerService} from '@/api/user'
import {useTokenStore} from "@/stores/token.js";
// 控制注册与登录表单的显示， 默认显示注册
const isRegister = ref(false)
const ruleForm = reactive({
  username: '',
  password: '',
  rePassword: ''
})
//自定义确认密码的校验函数
const rePasswordValid = (rule, value, callback) => {
  if (value == null || value === '') {
    return callback(new Error('请再次确认密码'))
  }
  if (ruleForm.password !== value) {
    return callback(new Error('两次输入密码不一致'))
  }
}
//用于注册的表单校验模型
const registerDataRules = ref({
  username: [
    {required: true, message: '请输入用户名', trigger: 'blur'},
    {min: 5, max: 16, message: '用户名的长度必须为5~16位', trigger: 'blur'}
  ],
  password: [
    {required: true, message: '请输入密码', trigger: 'blur'},
    {min: 5, max: 16, message: '密码长度必须为5~16位', trigger: 'blur'}
  ],
  rePassword: [
    {validator: rePasswordValid, trigger: 'blur'}
  ]
})
//清空数据模型的数据
const clearRegisterData = () => {
  ruleForm.value = {
    username: '',
    password: '',
    rePassword: ''
  }
}
const submitClick = async () => {
  const res = await registerService(ruleForm)
  ElMessage.success(res.message)
}
const router = useRouter()
//调用useTokenStore得到状态
const tokenStore = useTokenStore();
const loginClick = async () => {
  const res = await loginService(ruleForm)
  //保存token
  tokenStore.setToken(res.data)
  ElMessage.success(res.message)
  await router.push('/')
}
</script>

<template>
  <el-row class="login-page">
    <el-col :span="12" class="bg"></el-col>
    <el-col :span="6" :offset="3" class="form">
      <!-- 注册表单 -->
      <el-form ref="form" size="large" :model="ruleForm" autocomplete="off" :rules="registerDataRules"
               v-if="isRegister">
        <el-form-item>
          <h1>注册</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input :prefix-icon="User" v-model="ruleForm.username" placeholder="请输入用户名"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input :prefix-icon="Lock" v-model="ruleForm.password" type="password"
                    placeholder="请输入密码"></el-input>
        </el-form-item>
        <el-form-item prop="rePassword">
          <el-input :prefix-icon="Lock" v-model="ruleForm.rePassword" type="password"
                    placeholder="请输入再次密码"></el-input>
        </el-form-item>
        <!-- 注册按钮 -->
        <el-form-item>
          <el-button class="button" type="primary" auto-insert-space @click="submitClick">
            注册
          </el-button>
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = false; clearRegisterData()">
            ← 返回
          </el-link>
        </el-form-item>
      </el-form>
      <!-- 登录表单 -->
      <el-form ref="form" size="large" :model="ruleForm" autocomplete="off" v-else>
        <el-form-item>
          <h1>登录</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input :prefix-icon="User" v-model="ruleForm.username" placeholder="请输入用户名"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input name="password" :prefix-icon="Lock" v-model="ruleForm.password" type="password"
                    placeholder="请输入密码"></el-input>
        </el-form-item>
        <el-form-item class="flex">
          <div class="flex">
            <el-checkbox>记住我</el-checkbox>
            <el-link type="primary" :underline="false">忘记密码？</el-link>
          </div>
        </el-form-item>
        <!-- 登录按钮 -->
        <el-form-item>
          <el-button class="button" type="primary" @click="loginClick" auto-insert-space>登录</el-button>
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = true;clearRegisterData()">
            注册 →
          </el-link>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<style lang="scss" scoped>
/* 样式 */
.login-page {
  height: 100vh;
  background-color: #fff;

  .bg {
    background: url('@/assets/logo2.png') no-repeat 60% center / 240px auto,
    url('@/assets/login_bg.jpg') no-repeat center / cover;
    border-radius: 0 20px 20px 0;
  }

  .form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    user-select: none;

    .title {
      margin: 0 auto;
    }

    .button {
      width: 100%;
    }

    .flex {
      width: 100%;
      display: flex;
      justify-content: space-between;
    }
  }
}
</style>
