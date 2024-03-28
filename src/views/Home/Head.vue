<template>
  <header class="home-head-box">
    <div class="info">
      <div class="time">
        <span>{{ time.day }}</span>
        <span>{{ time.month }}</span>
      </div>
      <h2 class="title">我的知乎日报</h2>
    </div>
    <!-- 根据登录状态显示不同组件 -->
    <div class="picture">
      <router-link :to="getProfileLink()">
        <img :src="cat" alt="" />
      </router-link>
    </div>
  </header>
</template>
  
<script setup>
import { computed } from 'vue';
import { useRouter } from 'vue-router';
import cat from '@/assets/images/cat.jpg';

const props = defineProps({ today: String });
const route = useRouter();

const time = computed(() => {
  let [, month, day] = props.today.match(/^\d{4}(\d{2})(\d{2})$/),
    area = [
      '零', '一', '二', '三', '四', '五', '六', '七', '八', '九', '十', '十一', '十二'
    ]; 
  return {
    month: area[month] + '月',
    day
  };
});

const getProfileLink = () => {
  // 根据实际情况判断用户是否已登录
  const isLoggedIn = !!localStorage.getItem('isLoggedIn');

  // 根据登录状态返回不同的路径
  return isLoggedIn ? '/personal' : '/login';
};
</script>
  
<style lang="scss" scoped>
.home-head-box {
  box-sizing: border-box;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 30px;

  .picture {
    width: 80px;
    height: 80px;
    border-radius: 50%;
    overflow: hidden;

    img {
      display: block;
      width: 100%;
      height: 100%;
    }
  }

  .info {
    display: flex;
    align-items: center;

    .title {
      color:cornflowerblue;
      padding-left: 24px;
      height: 64px;
      line-height: 64px;
      font-size: 40px;
      border-left: 2px solid #EEE;
    }

    .time {
      padding-right: 30px;
      height: 70px;

      span {
        display: block;
        text-align: center;
        line-height: 35px;
        font-size: 24px;
        color: red;

        &:nth-child(1) {
          font-size: 32px;
        }
      }
    }
  }
}
</style>