<template>
    <div class="detail-box">
        <!-- 新闻内容  -->
        <div class="news-body" v-html="news.body"></div>
        <!-- 底部图标 -->
        <van-tabbar v-model="active" active-color="#ee0a24">
            <van-tabbar-item icon="home-o" @click="$router.go(-1)">返回</van-tabbar-item>
            <van-tabbar-item icon="good-job-o" :badge="extra.popularity">点赞</van-tabbar-item>
            <van-tabbar-item icon="like-o" :badge="extra.short_comments">评论</van-tabbar-item>
            <van-tabbar-item :icon="isFavorite ? 'star' : 'star-o'" @click="toggleFavorite">收藏</van-tabbar-item>
        </van-tabbar>
    </div>
</template>

<script setup>
import { useRoute } from 'vue-router';
import { ref, onMounted, onUnmounted, nextTick } from 'vue';
import http from '@/request/index';
import router from '@/router';

const route = useRoute();
const news = ref({});
const extra = ref({});
const isFavorite = ref(false);

let link = null;

const getNews = async (id) => {
    try {
        let result = await http.get('/api/news/' + id);
        news.value = result;
        nextTick(() => {
            handleStyle(result);
            handleImage(result);
        });
    } catch (_) { }
};

const handleStyle = (result) => {
    if (result == null) return;
    let { css } = result;
    if (!Array.isArray(css)) return;
    css = css[0];
    if (!css) return;
    // 创建<LINK>导入样式
    link = document.createElement('link');
    link.rel = 'stylesheet';
    link.href = css;
    document.head.appendChild(link);
};

const handleImage = (result) => {
    if (result == null) return;
    let imgPlaceHolder = document.querySelector('.img-place-holder');
    if (!imgPlaceHolder) return;
    // 创建大图
    let tempImg = new Image();
    tempImg.src = result.image;
    tempImg.onload = () => {
        imgPlaceHolder.appendChild(tempImg);
    };
    tempImg.onerror = () => {
        let parent = imgPlaceHolder.parentNode;
        parent.parentNode.removeChild(parent);
    };
};

onMounted(() => {
    getNews(route.params.id);
    checkFavoriteStatus();
});

onUnmounted(() => {
    if (link) document.head.removeChild(link);
});

async function getExtra() {
    let result = await http.get('/api/story-extra/' + route.params.id);
    extra.value = result;
}

onMounted(() => {
    getExtra();
});

function isLoggedIn() {
    return !!localStorage.getItem('isLoggedIn');
}

function checkFavoriteStatus() {
    const favoriteNewsIds = JSON.parse(localStorage.getItem('favoriteNewsIds')) || [];
    isFavorite.value = favoriteNewsIds.includes(route.params.id);
    console.log(route.params.id);
}

function toggleFavorite() {
    if (isLoggedIn()) {
        const favoriteNewsIds = JSON.parse(localStorage.getItem('favoriteNewsIds')) || [];
        const newsId = route.params.id;

        if (isFavorite.value) {
            // 如果已收藏，取消收藏
            const index = favoriteNewsIds.indexOf(newsId);
            if (index !== -1) {
                favoriteNewsIds.splice(index, 1);
                console.log("取消收藏");
            }
        } else {
            // 如果未收藏，添加到收藏列表
            favoriteNewsIds.push(newsId);
            console.log("收藏");
        }
        // 更新本地存储中的收藏列表
        localStorage.setItem('favoriteNewsIds', JSON.stringify(favoriteNewsIds));
        // 切换收藏状态
        isFavorite.value = !isFavorite.value;
    } else {
        // 如果用户未登录，跳转到登录页面
        router.push('/login');
    }
}
</script>

<!-- <style lang="scss">
.detail-box {
    .van-nav-bar {
        height: 50px;
    }
    .img-place-holder {
        max-height: 500px;
        //overflow: hidden;
        border-radius: 8px;
        img {
            width: 100%;
            height: 100%;
            border-radius: 8px;
        }
    }
    .meta {
        .avatar {
            width: 100px;
            margin: 10px 10px;
            border-radius: 50%;
        }
        .author {
            color: grey;
        }
        .bio{
            font-size: 20px;
            color: #fff;
        }
    }
    .question-title {
        display: block;
        color: black;
    }
    .news-body {
        flex: 1;
        overflow-y: scroll;
    }
    .van-tabbar {
        height: 120px;
    }
    font-size: 30px;
    overflow-x: scroll;
    margin: 15px;
    padding-bottom: 90px;
}
</style> -->

<style lang="scss">
.detail-box {

    // 顶部大图
    .img-place-holder {
        img {
            border-radius: 8px;
        }
    }

    // 用户
    .meta {
        margin-top: 150px;

        .avatar {
            width: 100px;
            height: 100px;
            margin: 10px 10px;
            border-radius: 50%;
        }

        .author {
            color: grey;
        }

        .bio {
            color: #fff;
        }
    }

    .content {
        margin-top: 100px;
    }

    .van-nav-bar {
        height: 50px;
    }

    .news-body {
        flex: 1;
        overflow-y: scroll;
    }

    .van-tabbar {
        height: 120px;
    }

    font-size: 30px;
    overflow-x: scroll;
    margin: 20px;
    padding-bottom: 90px;
}
</style>