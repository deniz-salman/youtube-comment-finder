<template>
  <div id="comment_find" class="flex flex-col items-center mb-32 p-4">
    <div class="card">
      <div class="ml-4">
        <span class="text-lg">Search the comments</span>
      </div>

      <div v-if="$root.video" class="p-4 flex flex-col sm:flex-row items-start">
        <img :src="$root.video.snippet.thumbnails.high.url" :alt="$root.video.snippet.title" class="video-thumbnail" />
        <div class="mt-2 sm:ml-4 w-full sm:w-3/4">
          <h3 class="video-title">{{ $root.video.snippet.title }}</h3>
          <p class="video-description">{{ $root.video.snippet.description }}</p>
          <a :href="`https://www.youtube.com/channel/${$root.video.snippet.channelId}`" target="_blank">
            <p class="video-channel">{{ $root.video.snippet.channelTitle }}</p>
          </a>
          <span
            class="bg-gray-100 text-gray-800 text-xs font-medium inline-flex items-center px-2.5 py-0.5 rounded me-2 dark:bg-gray-700 dark:text-gray-400 border border-gray-500">
            👍
            {{ parseInt($root.video.statistics.likeCount).toLocaleString() }}
          </span>
          <span
            class="bg-gray-100 text-gray-800 text-xs font-medium inline-flex items-center px-2.5 py-0.5 rounded me-2 dark:bg-gray-700 dark:text-gray-400 border border-gray-500">
            💬
            {{ parseInt($root.video.statistics.commentCount).toLocaleString() }}
          </span>
          <span
            class="bg-gray-100 text-gray-800 text-xs font-medium inline-flex items-center px-2.5 py-0.5 rounded me-2 dark:bg-gray-700 dark:text-gray-400 border border-gray-500">
            👁️
            {{ parseInt($root.video.statistics.viewCount).toLocaleString() }}
          </span>
        </div>
      </div>

      <div class="flex justify-center mx-4">
        <div class="relative w-full">
          <input type="text" placeholder="Search comment" @keyup.enter="searchComment" v-model="search"
            class="search-input" />
          <button @click="searchComment"
            class="absolute right-2 top-2 text-gray-500 hover:text-gray-700 focus:outline-none">
            🔍
          </button>
        </div>
      </div>

      <div v-if="$root.comments.length > 0" id="comment_list" class="mt-4">
        <span class="text-lg font-semibold m-4">🔍 {{ searched }}</span>
        <div class="overflow-y-auto max-h-[600px] p-4">
          <div class="grid grid-cols-1 gap-4">
            <Comment v-for="comment in $root.comments" :key="comment.id"
              :authorProfileImageUrl="comment.snippet.topLevelComment.snippet.authorProfileImageUrl"
              :channelIdValue="comment.snippet.topLevelComment.snippet.authorChannelId.value"
              :authorDisplayName="comment.snippet.topLevelComment.snippet.authorDisplayName"
              :textDisplay="comment.snippet.topLevelComment.snippet.textDisplay"
              :likeCount="comment.snippet.topLevelComment.snippet.likeCount"
              :totalReplyCount="comment.snippet.totalReplyCount" :videoId="$root.video.id.videoId"
              :publishedAt="comment.snippet.topLevelComment.snippet.publishedAt"
              :commentId="comment.snippet.topLevelComment.id">
            </Comment>
          </div>
        </div>
      </div>
    </div>

    <Alert :isShow="showNoCommentsAlert" @update:isShow="showNoCommentsAlert = $event" message="No comments found." />
  </div>

</template>

<script>

import Alert from './Alert.vue';
import Comment from './Comment.vue';

export default {
  name: 'CommentSearch',
  data() {
    return {
      search: '',
      searched: '',
      showNoCommentsAlert: false
    }
  },
  mounted() {
    this.$root.$watch('video', () => {
      this.search = "";
    });
  },
  components: {
    Comment,
    Alert
  },
  methods: {
    searchComment() {
      if (!this.search) return;

      const url = `https://www.googleapis.com/youtube/v3/commentThreads?part=snippet&videoId=${this.$root.video.id.videoId}&maxResults=100&searchTerms=${this.search}&textFormat=plainText&key=${process.env.VUE_APP_YT_API_KEY}`;

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
            this.$root.comments = data.items;
            console.log(this.$root.comments);
            this.showNoCommentsAlert = false;
            setTimeout(() => {
              const commentListElement = document.getElementById('comment_list');
              if (commentListElement) {
                window.scrollTo({
                  top: commentListElement.offsetTop - 100,
                  left: 0,
                  behavior: 'smooth',
                });
              }
            }, 10);
          } else {
            this.$root.comments = [];
            this.showNoCommentsAlert = true;
          }
        })
        .catch(error => {
          console.error('Error:', error);
          this.$root.comments = [];
          this.showNoCommentsAlert = true;
        });
    },

  }
}
</script>
