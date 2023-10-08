<!--
This example fetches latest Vue.js commits data from GitHub’s API and displays them as a list.
You can switch between the two branches.
-->

<script setup>
import { computed, ref, watchEffect } from 'vue'

const API_URL = `https://api-berita-indonesia.vercel.app/antara/terbaru/`
const news = ref(null)
const newsFilter = computed(() => {
    return news.value.data.posts.filter((post, index) => index < 5)
})

watchEffect(async () => {
    const url = `${API_URL}`
    news.value = await (await fetch(url)).json()
})
</script>

<template>
    <div class="flex items-center justify-center py-4 md:py-8 flex-wrap">
        <div
            class="block max-w-xl p-6 bg-white border border-gray-200 rounded-lg shadow hover:bg-gray-100 dark:bg-gray-800 dark:border-gray-700 dark:hover:bg-gray-700">
            <h2
                class="mb-4 text-sm font-extrabold leading-none tracking-tight text-gray-900 md:text-5xl lg:text-2xl dark:text-white">
                Latest News</h2>
            <ul role="list" class="divide-y divide-gray-100">
                <li v-for="(post, index) in newsFilter" :key="index" class="flex justify-between gap-x-6 py-5">
                    <div class="flex min-w-0 gap-x-4">
                        <img class="h-12 w-12 flex-none bg-gray-50" :src="post.thumbnail" alt="" />
                        <div class="min-w-0 flex-auto">
                            <p class="text-sm font-semibold leading-6 text-gray-900">{{ post.title }}</p>
                            <p class="mt-1 truncate text-xs leading-5 text-gray-500"><a :href="post.link"
                                    target="_blank">Read More</a></p>
                        </div>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<style>
a {
    text-decoration: none;
    color: #42b883;
}

li {
    line-height: 1.5em;
    margin-bottom: 20px;
}

.author,
.date {
    font-weight: bold;
}
</style>