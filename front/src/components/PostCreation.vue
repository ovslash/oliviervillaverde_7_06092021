<!-- outil de creation de post -->

<template>
  <div>
    <div v-if="mode == 'default'">
      <b-button @click="changeMode" type="button" class="mb-3">
        Pour créer une publication
      </b-button>
    </div>
    <div v-if="mode == 'edit'">
      <b-card class="shadow mb-3"
        ><b-form @submit.prevent="createPost">
          <b-form-textarea
            placeholder="Ecrivez quelque chose ..."
            rows="3"
            v-model="description"
            title="Créer une publication"
          ></b-form-textarea>

          <PostInputMedia
            labelTitle="Ajouter des images"
            inputImageId="input-image"
            inputImageClass="input-file"
            previewMedia=".preview-media"
            inputFile=".input-file"
          />
          <div class="preview-media"></div>
          <p class="text-danger small">{{ errorMessage }}</p>
          <b-button class="m-1" @click="cancel" type="reset">Annuler</b-button>
          <b-button class="m-1" type="submit">Publier</b-button>
        </b-form></b-card
      >
    </div>
  </div>
</template>

<script>
import PostInputMedia from "../components/PostInputMedia.vue";
import { apiFetch } from "../utils/ApiFetch";
import { eventBus } from "../main.js";

export default {
  name: "PostCreation",
  components: {
    PostInputMedia,
  },
  data() {
    return {
      userData: JSON.parse(localStorage.getItem("userData")),
      description: "",
      errorMessage: "",
      mode: "default",
    };
  },

  //-------------------------------------------------------------------------------
  methods: {
    cancel() {
      this.mode = "default";
    },

    changeMode() {
      this.mode = "edit";
    },
    createPost() {
      const description = this.description;
      const userId = this.userData.id;

      const inputFile = document.querySelector(".input-file");
      const images = inputFile.files;

      //const form = document.querySelector(".create-form");

      if (description !== "" || images.length !== 0) {
        this.errorMessage = "";
        const isFormData = images.length > 0;

        let body = {
          userId: Number(userId),
          description: description,
        };

        if (isFormData) {
          const formData = new FormData();
          for (let i = 0; i < images.length; i++) {
            formData.append("image", images[i]);
          }
          formData.append("post", JSON.stringify(body));
          body = formData;
        }

        apiFetch
          .post("/posts/", body, { isFormData })
          .then(() => {
            this.removePreviewChild();
            this.description = "";
            //form.reset();
            eventBus.$emit("loadPosts");
            this.mode = "default";
          })
          .catch((error) => {
            console.log(error);
            this.errorMessage = "Une erreur est survenue";
          });
      } else {
        this.errorMessage = "Vous ne pouvez pas créer une publication vide";
      }
    },

    removePreviewChild() {
      const previewMedia = document.querySelector(".preview-media");
      while (previewMedia.firstChild) {
        previewMedia.removeChild(previewMedia.firstChild);
      }
    },
  },
};
</script>
