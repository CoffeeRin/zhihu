<template>
    <van-nav-bar title="我的收藏" left-arrow @click-left="goBack"/>
    <div class="collection">
        <van-list v-if="favoriteNews.length > 0">
            <van-cell v-for="news in favoriteNews" @click="viewNewsDetail(news)">
                <template #icon>
                    <img :src="news.image" alt="news image" style="width: 64px; height: 64px; border-radius: 8px;">
                </template>
                <p class="title">{{ news.title }}</p>
            </van-cell>
        </van-list>
        <van-empty v-else description="暂无收藏" />
    </div>
</template>
  
<script>
import { ref, onMounted } from 'vue';
import http from '@/request/index';
import router from '@/router';

export default {
    setup() {
        const favoriteNews = ref([]); // 用于存储收藏的文章列表

        // 获取本地已收藏文章ID列表
        const favoriteNewsIds = JSON.parse(localStorage.getItem('favoriteNewsIds')) || [];
        console.log(favoriteNews);
        // 获取收藏的文章列表
        const getFavoriteNews = async () => {
            try {
                const promises = favoriteNewsIds.map(async (newsId) => {
                    const result = await http.get(`/api/news/${newsId}`);
                    return result;
                });

                const newsArray = await Promise.all(promises);
                favoriteNews.value = newsArray.filter((news) => news !== null);
            } catch (error) {
                console.error('Error fetching favorite news:', error);
            }
        };

        // 页面加载时获取收藏的文章列表
        onMounted(() => {
            getFavoriteNews();
        });

        // 返回上一页
        const goBack = () => {
            router.go(-1);
        };

        // 查看文章详情
        const viewNewsDetail = (news) => {
            // 跳转到文章详情页的逻辑
            router.replace(`/detail/${news.id}`);
        };

        //通过 return 语句将需要在模板中使用的数据和方法暴露出去，使得它们可以被模板访问
        return { favoriteNews, goBack, viewNewsDetail };
    },
};
</script>
  
<style lang="scss" scoped>
.collection {
    margin-top: 50px;
}

.van-nav-bar {
    --van-nav-bar-arrow-size:30px;
    --van-nav-bar-height:80px;
    --van-nav-bar-title-font-size:20px;
}

.title {
    margin: 0px 20px;
    height: 90px;
    font-size: 32px;
    color: #999;
    max-height: 90px;
    line-height: 45px;
    overflow: hidden;
    text-align: left;
}
</style>
  