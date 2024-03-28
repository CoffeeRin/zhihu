<template>
  <div class="login-container">
    <!-- 返回主页面按钮 -->
    <van-nav-bar title="登录页" left-text="返回" left-arrow @click-left="this.$router.replace('/')" />
    <div class="input">
      <van-form @submit="login">
        <!-- 显示用户图片 -->
        <img src="@/assets/images/cat.jpg" class="avatar-image">
        <van-field v-model="user.phoneNumber" label="手机号" placeholder="请输入手机号" input-align="" />
        <van-field v-model="user.verificationCode" label="验证码" placeholder="请输入验证码" input-align="">
          <template #button>
            <van-button type="primary" @click="generateVerificationCode" size="small">获取验证码</van-button>
          </template>
        </van-field>
        <van-button type="primary" native-type="submit" block>登录</van-button>
      </van-form>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

export default {
  setup() {
    const router = useRouter();
    const user = ref({
      phoneNumber: '',
      verificationCode: '',
      avatar: '@/assets/images/timg.jpg',
    });

    const generateVerificationCode = () => {
      const code = Math.floor(1000 + Math.random() * 9000).toString();
      user.value.verificationCode = code;
    };

    const login = () => {
      //本地存储用户信息
      localStorage.setItem('user', JSON.stringify(user.value));
      localStorage.setItem('isLoggedIn', 'true');

      // Redirect using router
      router.push('/personal');
    };

    return { user, generateVerificationCode, login };
  },
};
</script>

<style lang="scss" scoped>
.van-nav-bar {
  --van-nav-bar-arrow-size: 30px;
  --van-nav-bar-height: 80px;
  --van-nav-bar-title-font-size: 20px;
}

.login-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;

  .input {
    margin-top: 100px;
  }
}

.avatar-image {
  max-width: 100px;
  max-height: 100px;
  border-radius: 50%;
  margin: 60px auto;
  display: block;
}</style>
