<template>
    <div class="flex justify-center mt-32 p-4">
        <div class="flex flex-col w-full max-w-3xl space-y-4">
            <div class="card" id="video_search">
                <div class="justify-center m-4">
                    <div class="mb-2 [text-shadow:_0_1px_0_rgb(0_0_0_/_40%)]">
                        <span class="text-lg ">Search for a Video</span>
                    </div>
                    <div class="relative w-full">
                        <input type="text" placeholder="Search for a video or enter a video URL"
                            @keyup.enter="searchVideo" v-model="search" class="search-input" />
                        <button @click="searchVideo"
                            class="absolute right-2 top-2 text-gray-500 hover:text-gray-700 focus:outline-none">
                            🔍
                        </button>
                    </div>
                </div>
                <div v-if="videos.length" id="video_list">
                    <span class="text-lg font-semibold m-4"> 🔍 {{ searched }}</span>
                    <div class="overflow-y-auto max-h-[600px] rounded-lg p-4">
                        <div class="grid grid-cols-1 gap-4">
                            <div v-for="video in videos" :key="video.id.videoId"
                                class="rounded-lg overflow-hidden flex border p-4 shadow-md">
                                <img :src="video.snippet.thumbnails.high.url" :alt="video.snippet.title"
                                    class="video-thumbnail" />
                                <div class="p-2 w-3/4 h-42">
                                    <h3 class="video-title">{{ video.snippet.title }}</h3>
                                    <p class="video-description">{{ video.snippet.description }}</p>
                                    <a :href="`https://www.youtube.com/channel/${video.snippet.channelId}`"
                                        target="_blank">
                                        <p class="video-channel">{{ video.snippet.channelTitle }}</p>
                                    </a>
                                    <button @click="selectVideo(video)" class="button">Search this
                                        video</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

        </div>
        <Alert :isShow="showNoVideosAlert" @update:isShow="showNoVideosAlert = $event" message="No videos found." />
        <Alert :isShow="connectionErrorAlert" @update:isShow="connectionErrorAlert = $event"
            message="Connection error. Please try again later." />
    </div>
</template>

<script>

import Alert from './Alert.vue';

export default {
    data() {
        return {
            search: '',
            searched: '',
            videos: [],
            showNoVideosAlert: false,
            connectionErrorAlert: false
        }
    },
    components: {
        Alert
    },
    mounted() {

    },
    methods: {
        searchVideo() {
            if (!this.search) return;
            const url = `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=10&q=${this.search}&type=video&key=${process.env.VUE_APP_YT_API_KEY}`;
            fetch(url)
                .then(response => {
                    this.searched = this.search;
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    return response.json();
                })
                .then(data => {
                    if (data.items && data.items.length > 0) {
                        if (this.searched.includes("v=")) {
                            this.videos = [];
                            this.selectVideo(data.items[0]);
                        }
                        else if (data.items.length == 1) {
                            this.selectVideo(data.items[0]);
                        }
                        else {
                            this.videos = data.items;
                        }
                        this.$root.comments = [];
                        this.$root.video = null;
                        setTimeout(() => {
                            const videoListElement = document.getElementById('video_list');
                            if (videoListElement) {
                                window.scrollTo({
                                    top: videoListElement.offsetTop - 100,
                                    left: 0,
                                    behavior: 'smooth',
                                });
                            }
                        }, 100);
                    } else {
                        this.videos = [];
                        this.$root.comments = [];
                        this.$root.video = null;
                        this.showNoVideosAlert = true;
                    }
                })
                .catch(error => {
                    this.videos = [];
                    this.$root.comments = [];
                    this.$root.video = null;
                    this.connectionErrorAlert = true;
                    console.error('Error:', error);
                });
        },
        selectVideo(video) {
            let videoObject = { ...video };
            const url = `https://www.googleapis.com/youtube/v3/videos?part=statistics&id=${video.id.videoId}&key=${process.env.VUE_APP_YT_API_KEY}`;

            fetch(url)
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    return response.json();
                })
                .then(data => {
                    videoObject.statistics = data.items[0].statistics;
                    this.$root.video = videoObject;
                    this.$root.comments = [];
                    this.$nextTick(() => {
                        console.log(this.$root.video);
                        const commentFindElement = document.getElementById('comment_find');
                        if (commentFindElement) {
                            window.scrollTo({
                                top: commentFindElement.offsetTop - 100,
                                left: 0,
                                behavior: 'smooth',
                            });
                        }
                    });
                })
                .catch(error => {
                    this.connectionErrorAlert = true;
                    console.error('Error:', error);
                });
        }
    }
}
</script>

<style scoped>
.container {
    max-width: 800px;
}
</style>