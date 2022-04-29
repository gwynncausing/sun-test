<template>
  <v-app>
    <v-main class="main">
      <v-text-field
        label="Search"
        v-model="search"
        @keypress.enter="searchApi(search)"
        outlined
        clearable
        class="mt-6 mx-8"
      ></v-text-field>

      <div class="d-flex">
        <div class="col-1 d-flex flex-column pa-8">
          <iframe
            v-if="currentlyPlaying.id"
            width="560"
            height="315"
            :src="youtubeUrl + currentlyPlaying.id.videoId + '?autoplay=1'"
            title="YouTube video player"
            frameborder="0"
            allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
            allowfullscreen
          ></iframe>

          <div v-if="currentlyPlaying.snippet" class="mt-4">
            <h1>{{ currentlyPlaying.snippet.title }}</h1>
            {{ currentlyPlaying.snippet.description }}
          </div>
        </div>

        <div class="col-2 pt-5">
          <v-card
            v-for="video in listVideos"
            :key="video.id.videoId"
            class="ma-3 d-flex"
            max-width="344"
            height="84"
            outlined
            @click="goToVideo(video)"
          >
            <img
              :src="video.snippet.thumbnails.default.url"
              :alt="video.snippet.title"
              class="pr-2"
              width="120"
              style="height: fit-content"
            />
            <p class="pa-2 truncate-3 ma-0">{{ video.snippet.title }}</p>
          </v-card>
        </div>
      </div>
    </v-main>
  </v-app>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      search: "",
      searchUrl: "https://www.googleapis.com/youtube/v3/search",
      // youtubeUrl: "https://www.youtube.com/watch?v=",
      youtubeUrl: "https://www.youtube.com/embed/",
      key: "AIzaSyC2gObk9zt96f2kbLl0ilTl2B9SFb8_RJM",
      items: [],
      currentlyPlaying: {},
      listVideos: [],
      videoKey: 0,
    };
  },

  async mounted() {
    this.searchApi("");
  },

  watch: {
    currentlyPlaying: {
      handler() {
        this.videoKey++;
      },
      immediate: true,
    },
  },

  methods: {
    async searchApi(searchText) {
      const result = await axios.get(this.searchUrl, {
        params: { part: "snippet", key: this.key, q: searchText },
      });

      this.items = result.data.items;
      this.currentlyPlaying = this.items[0];
      this.listVideos = this.items.splice(1);
    },

    async goToVideo(video) {
      this.currentlyPlaying = video;
      this.videoKey++;

      const result = await axios.get(this.searchUrl, {
        params: { part: "snippet", key: this.key, q: video.snippet.title },
      });

      this.items = result.data.items;
      this.listVideos = this.items.splice(1);
    },
  },
};
</script>

<style lang="scss">
.main {
  .col-1 {
    flex-grow: 1;
  }
  .col-2 {
    width: 600px;
  }
}

.truncate-3 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
}
h1 {
  font-size: 1.2em;
}
</style>
