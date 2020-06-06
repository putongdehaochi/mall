<template>
    <div class="recommends">
        <div v-for="(item, index) in recommends" :key="index" class="recommends-item">
            <a :href="item.link"> <img :src="item.image" alt="">
            </a>
            <div>{{item.title}}</div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'HomeRecommendView',
        props: {
            recommends: {
                type: Array,
                default () {
                    return []
                }
            }
        }

    }
</script>


<style scoped>
    .recommends {
        display: flex;
        width: 100%;
        text-align: center;
        font-size: 12px;
        padding: 15px 0 30px ;
        border-bottom: 10px solid #eee;
    }

    .recommends-item {
        flex: 1
    }

    .recommends-item img {
        width: 70px;
        height: 70px;
        margin-bottom: 10px;
    }
</style>