<!-- page d'accueil du site / login -->

<template>
  <div>
    <HeaderStarter />
    <b-container>
      <b-row>
        <b-col class="d-sm-flex flex-column align-items-center text-center">
          <h1>Réseau social</h1>

          <div>
            <h2>Connexion</h2>
            <b-form class="form-width my-3" @submit.prevent="login">
              <b-form-group class="mb-3">
                <div class="input-div" id="email">
                  <label for="login-email">Email</label>
                  <b-form-input
                    v-model="email"
                    type="email"
                    id="login-email"
                  ></b-form-input>
                </div>
              </b-form-group>

              <b-form-group class="mb-3">
                <div class="input-div" id="password">
                  <label for="login-password">Mot de passe</label>
                  <b-form-input
                    v-model="password"
                    type="password"
                    id="login-password"
                  ></b-form-input>
                </div>
              </b-form-group>
              <p class="text-danger small">{{ errorMessage }}</p>
              <b-button type="submit">Connexion</b-button>
            </b-form>
          </div>

          <div class="mb-3">
            <h2>Pas encore de compte ?</h2>
            <p>Vous pouvez vous inscrire en quelques secondes.</p>
            <b-button @click="signup">S'inscrire</b-button>
          </div>
        </b-col>
      </b-row>
    </b-container>
    <FooterMainView />
  </div>
</template>

<script>
import router from "../router/index";
import { apiFetch } from "../utils/ApiFetch";
import HeaderStarter from "../components/HeaderStarter.vue";
import FooterMainView from "../components/FooterMainView.vue";

export default {
  name: "UserLogin",

  components: {
    HeaderStarter,
    FooterMainView,
  },

  data() {
    return {
      email: "",
      password: "",
      errorMessage: "",
    };
  },
  methods: {
    login() {
      if (this.email != "" && this.password !== "") {
        apiFetch
          .post("/auth/login", { email: this.email, password: this.password })
          .then((res) => {
            if (!res.token) {
              this.errorMessage = res.error;
            } else {
              localStorage.setItem("userToken", res.token);
              localStorage.setItem("userData", JSON.stringify(res.user));
              console.log("userToken:", res.token);
              router.push({ name: "groupomania" });
            }
          })
          .catch((error) => {
            console.log(error);
            this.errorMessage = "Erreur de connexion.";
          });
      } else {
        this.errorMessage =
          "Veuillez saisir votre email et votre mot de passe.";
      }
    },
    signup() {
      router.push({ name: "enregistrement" });
    },
  },
};
</script>
