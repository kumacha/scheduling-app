<template>
  <v-container>
    <v-row justify="center">
      <v-col sm="12" md="5">
        <h2 class="text-center subtitle-1 font-weight-bold mb-2">
          メールアドレスでログイン
        </h2>
        <v-row>
          <v-col>
            <v-tabs
              v-model="tab"
              background-color="transparent"
              color="#2BB7A4"
              grow
              class="mb-3"
            >
              <v-tab to="/login">ログイン</v-tab>
              <v-tab to="/register">アカウント登録</v-tab>
            </v-tabs>
            <v-row>
              <v-col sm="12">
                <v-card flat>
                  <v-card-text class="pa-0">
                    <v-form
                      ref="login_form"
                      v-model="login_valid"
                      lazy-validation
                    >
                      <v-text-field
                        v-model="login_email"
                        label="メールアドレス"
                        required
                      />
                      <v-text-field
                        v-model="login_password"
                        label="パスワード"
                        required
                        :append-icon="
                          show_loginpassword ? 'mdi-eye' : 'mdi-eye-off'
                        "
                        :type="show_loginpassword ? 'text' : 'password'"
                        @click:append="show_loginpassword = !show_loginpassword"
                      />
                      <v-alert v-if="loginErrorMsg" dense text type="error">
                        {{ loginErrorMsg }}
                      </v-alert>

                      <div id="login-btn">
                        <v-btn
                          :disabled="!login_valid"
                          color="#2BB7A4"
                          class="my-4 white--text"
                          justify="center"
                          @click="email_login"
                        >
                          ログイン
                        </v-btn>
                      </div>
                      <div id="github_login">
                        <v-btn
                          :disabled="!login_valid"
                          color="black"
                          class="my-4 white--text"
                          justify="center"
                          @click="github_login"
                        >
                          Githubでログイン
                        </v-btn>
                      </div>
                    </v-form>
                  </v-card-text>
                </v-card>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
import firebase from '~/plugins/firebase';
export default {
  layout({ store }) {
    return store.state.loggedIn ? 'default' : 'home';
  },
  data() {
    return {
      tab: null,
      login_valid: true,
      login_email: '',
      login_password: '',
      show_loginpassword: false,
      loginErrorMsg: '',
      socialLoginErrorMsg: '',
    };
  },
  methods: {
    email_login() {
      this.$store
        .dispatch('email_login', {
          email: this.login_email,
          password: this.login_password,
        })
        .then(() => {
          this.login_email = '';
          this.login_password = '';
        })
        .catch((err) => {
          if (err.code === 'auth/user-disabled') {
            this.loginErrorMsg = 'このアカウントはロックされています。';
          } else {
            this.loginErrorMsg =
              'メールアドレスまたはパスワードが間違っています。';
          }
        });
    },
    github_login() {
      const provider = new firebase.auth.GithubAuthProvider();
      provider.addScope('repo');
      provider.setCustomParameters({
        allow_signup: 'true',
      });
      firebase
        .auth()
        .signInWithPopup(provider)
        .then((result) => {
          this.$router.push({
            name: 'index',
            params: {
              dashboard_msg: true,
              dashboard_msg_text: 'ログイン処理が完了しました。',
            },
          });

          const credential = result.credential;

          const token = credential.accessToken;
          console.log(token);

          // The signed-in user info.
          const user = result.user;
          console.log(user);
          // ...
        })
        .catch((error) => {
          // エラーまとめ
          const errorCode = error.code;
          const errorMessage = error.message;
          console.log(errorCode);
          console.log(errorMessage);
          // エラーの元凶のURLを確認
          const email = error.email;
          console.log(email);
          const credential = error.credential;
          console.log(credential);
          // ...
        });
    },
  },
};
</script>

<style scoped></style>
