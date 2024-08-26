<template>
  <Nav />
  <VideoSearch />
  <CommentSearch v-if="$root.video && $root.video.statistics" />
</template>

<script>
import VideoSearch from './components/VideoSearch.vue';
import CommentSearch from './components/CommentSearch.vue';
import Nav from './components/Nav.vue';
export default {
  name: 'App',
  mounted() {
    setTimeout(() => {
      window.scrollTo({
        top: 0,
        left: 0,
        behavior: 'smooth',
      });
    }, 1);
  },
  data() {
    return {
      video: null,
      comments: [],
    }
  },
  components: {
    Nav,
    VideoSearch,
    CommentSearch
  }
}
</script>
