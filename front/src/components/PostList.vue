<!-- la liste des publications -->

<template>
  <div>
    <div v-if="posts.length">
      <b-card-group v-for="post in posts" :key="post.updatedAt">
        <b-card
          header-tag="header"
          footer-tag="footer"
          class="mb-3 shadow"
          header-bg-variant="default"
          footer-bg-variant="default"
        >
          <template #header>
            <PostHeader :post="post" :loadPosts="loadPosts" />
          </template>

          <PostContent :post="post" :loadPosts="loadPosts" />
          <template #footer>
            <PostFooter :post="post" />
          </template>
        </b-card>
      </b-card-group>
    </div>

    <div v-else-if="!posts.length && !noPost">
      <LoadSpinner />
    </div>

    <div v-if="noPost">
      <p class="my-4 center">Pas encore de publication.</p>
      <p class="my-4 center">Vous serez peu être le premier à publier !</p>
    </div>
  </div>
</template>

<script>
import PostContent from "../components/PostContent.vue";
import PostHeader from "../components/PostHeader.vue";
import PostFooter from "../components/PostFooter.vue";
import { apiFetch } from "../utils/ApiFetch";
import { eventBus } from "../main.js";
import LoadSpinner from "../components/LoadSpinner.vue";

export default {
  name: "PostList",
  components: {
    PostContent,
    PostHeader,
    PostFooter,
    LoadSpinner,
  },
  data() {
    return {
      posts: [],
      noPost: false,
    };
  },
  props: {
    userId: { type: Number },
  },
  mounted() {
    this.loadPosts();
    eventBus.$on("loadPosts", () => {
      this.loadPosts();
    });
  },
  methods: {
    loadPosts() {
      let userIdParams = "";
      if (this.userId) {
        userIdParams = `?userId=${this.userId}`;
      }
      apiFetch
        .get(`/posts/${userIdParams}`)
        .then((data) => {
          this.posts = data.posts;
          if (data.posts.length === 0) {
            this.noPost = true;
          } else {
            this.noPost = false;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>
