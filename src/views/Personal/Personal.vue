<template>
  <div>
    <van-nav-bar title="个人详情页" left-text="返回" left-arrow @click-left="goBack" right-text="退出登录" @click-right="logout" />
    <div class="user-details-item">
      <img src="@/assets/images/cat.jpg" alt="" class="avatar-image" style="vertical-align: middle;">
      <p class="user-details-name">Coffee.Lin</p>
      <p class="user-details-label">手机号:<span class="phoneNumber">{{ user.phoneNumber }}</span></p>
      <!-- <img v-if=user.avatar :src=user.avatar alt="Avatar" class="avatar-image" />
        <span v-else>暂无头像</span> -->
    </div>
    <van-cell-group inset>
      <van-cell center title="我的收藏" size="normal" is-link to="/collection" />
      <van-cell center title="退出登录" size="normal" is-link @click="logout" />
    </van-cell-group>
  </div>
</template>

<script>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

export default {
  setup() {
    const router = useRouter();
    const user = ref(JSON.parse(localStorage.getItem('user')) || {});

    const goBack = () => {
      if (isLoggedIn()) {
        // 如果已登录，则返回到主页面
        router.replace('/');
      } else {
        // 否则，返回上一页
        router.go(-1);
      }
    };

    const logout = () => {
      console.log("Logging out...");
      localStorage.removeItem('user');
      localStorage.removeItem('isLoggedIn');
      localStorage.removeItem('favoriteNewsIds');

      // 使用路由进行跳转到登录页
      router.replace('/login');
      console.log("Logged out successfully.");
    };

    const isLoggedIn = () => {
      // 根据实际情况判断用户是否已登录
      return !!localStorage.getItem('isLoggedIn');
    };

    return { user, goBack, logout };
  },
};
</script>

<style lang="scss" scoped>
.van-nav-bar {
  --van-nav-bar-arrow-size: 30px;
  --van-nav-bar-height: 80px;
  --van-nav-bar-title-font-size: 20px;
}

.phoneNumber {
  margin: 50px 0;
  font: fold;
  color: cornflowerblue;
}

.van-cell {
  font-size: 30px;
}

.user-details-item {
  margin-bottom: 10px;
  margin-bottom: 100px;

  .user-details-name {
    text-align: center;
    color: #000;
  }

  .avatar-image {
    max-width: 300px;
    max-height: 300px;
    border-radius: 50%;
    margin: 10px auto;
    display: block;
  }

  .user-details-label {
    // font-weight: bold;
    color: grey;
    margin-bottom: 5px;
    margin-left: 30px;
  }
}
</style>
