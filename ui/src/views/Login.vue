<template>
  <v-form v-model="valid">
    <v-container fluid fill-height>
      <v-layout align-center justify-center>
        <v-flex xs12 sm8 md4>
          <v-card class="elevation-12">
            <v-toolbar dark color="primary" outlinedZ>
              <v-toolbar-title>Sign In</v-toolbar-title>
            </v-toolbar>
            <v-card-text>
              <v-form @submit.prevent="login()">
                <v-text-field
                  prepend-icon="mdi-account"
                  label="Username"
                  outlined
                  v-model="username"
                  autofocus
                ></v-text-field>
                <v-text-field
                  id="password"
                  prepend-icon="mdi-lock"
                  label="Password"
                  type="password"
                  outlined
                  v-model="password"
                ></v-text-field>
              
              <v-alert
                v-if="alert"
                border="top"
                colored-border
                close-text="Close Alert"
                color="red"
                dismissible
              >
                {{ alert }}
              </v-alert>
              <v-spacer></v-spacer>
              <v-btn
                color="success"
                :loading="loading"
                :disabled="loading"
                type="submit"
                text
                class="mx-r"
              >
                Login</v-btn
              >
            </v-form>
            </v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
  </v-form>
</template>

<script>
export default {
  name: "Login",
  data: () => ({
    username: null,
    password: null,
    loading: false,
    valid: false,
    alert: "",
  }),
  methods: {
    login() {
      this.loading = true;
      let formData = {
        username: this.username,
        password: this.password,
      };
      this.alert = "";
      this.globalFunctions
        .httpRequest(
          "post",
          `${this.apiUrl}/api/v1/authentication/get_token/`,
          formData
        )
        .then((res) => {
          this.loading = false;
          if (res.status != 200) {
            if(res.data.non_field_errors) {
              this.alert = res.data.non_field_errors[0]
            }else {
              this.alert = JSON.stringify(res.data);
            }            
          } else {
            localStorage.setItem("mapping_portal_access_token", res.data.token);
            this.$router.push(this.$route.query.redirect || '/')
          }
        });
    },
  },
};
</script>

<style>
</style>