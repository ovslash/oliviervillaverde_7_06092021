<!-- contenu de la publication -->

<template>
  <!-- CONTENU PUBLICATION -->
  <b-row>
    <b-col>
      <b-card-text class="post-content">
        <!-- texte -->
        <p class="text-break text-left">{{ post.description }}</p>
        <!-- images -->
        <PostImages :post="post" v-if="imageUrl.length > 0" />
      </b-card-text>
    </b-col>
  </b-row>
</template>

<script>
import PostImages from "./PostImages.vue";

export default {
  name: "PostContent",
  components: {
    PostImages,
  },
  props: {
    post: { type: Object },
  },
  data() {
    return {
      imageUrl: JSON.parse(this.post.imageUrl),
    };
  },
};
</script>

<style>
.post-content {
  white-space: pre-wrap;
}
</style>
