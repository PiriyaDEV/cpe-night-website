<template>
  <div>
    <div v-if="page === 1" class="two-background">
      <vue-particles
        color="#dedede"
        :particleOpacity="0.3"
        :particlesNumber="60"
        shapeType="circle"
        :particleSize="3"
        :lineLinked="false"
        :moveSpeed="1.5"
        :hoverEffect="false"
        hoverMode="grab"
        :clickEffect="true"
        clickMode="push"
      ></vue-particles>
      <div class="row page-container" id="login-container">
        <div id="right-side">
          <img id="cpe-logo" src="@/assets/Logo.png" />
        </div>
        <div>
          <div class="section">
            <div>
              <div style="display: flex; align-items: center">
                <a
                  @click="backMain()"
                  style="text-decoration: none; cursor: pointer;"
                  id="backToMain"
                >&lt; {{$t('general.backtomain')}}</a>
                <img
                  @click="backMain()"
                  style="padding-left: 10px; width: 23px; margin-top: -3.5px; cursor: pointer;"
                  src="@/assets/user/home.png"
                />
              </div>
              <div id="left-side" class="column" style>
                <div>
                  <h1 style="cursor: default;" :class=checkLang >{{$t('general.loginThai')}}</h1>
                  <!-- Input -->
                  <div class="box">
                    <img id="img-user" src="@/assets/icons8-male-user-64.png" />
                    <input
                      v-model="user.email"
                      class="input"
                      type="text"
                      placeholder="Email"
                      required
                    />
                  </div>

                  <hr class="underline" />

                  <div class="box">
                    <img id="img-lock" src="@/assets/icons8-lock-52.png" />
                    <input
                      style="margin-left:16px;"
                      v-model="user.password"
                      class="input"
                      type="password"
                      placeholder="Password"
                      required
                    />
                  </div>

                  <hr class="underline" />

                  <p @click="forgetCheck()" id="forgot" style="cursor: pointer;">{{$t('loginRegister.forgotPass')}}</p>
                  <br />
                  <button class="grow-on-hover" @click="checkInput()" id="loginButton" style="cursor: pointer;">{{$t('general.loginThai')}}</button>
                  <br />

                  <div id="register-section">
                    <span
                      class="descript-text"
                      style="color: #ffffff; cursor: default;"
                    >{{$t('loginRegister.dontHaveAccount')}}</span>
                    <router-link
                      to="/register"
                      class="descript-text"
                      style="margin-left: 10px; color: #f28093"
                    >{{$t('loginRegister.registerText')}}</router-link>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div id="forgetPass" v-if="page == 2">
      <ForgetPass @pageReturn="pageReturn" />
    </div>
  </div>
</template>

<script>
import User from "../models/user";
import ForgetPass from "../components/forgetPass";

export default {
  name: "login_page",
  components: {
    ForgetPass,
  },
  data() {
    return {
      user: new User(),
      loading: false,
      message: "",
      page: 1,
      googleAuth: {
        params: {
          client_id:
            "691961416858-9n4gn62s613l3v6ubp3g562e5b6brp1b.apps.googleusercontent.com"
        }
      }
    };
  },
  computed: {
    loggedIn() {
      return this.$store.state.auth.status.loggedIn;
    },
    checkLang() {
        if(localStorage.getItem("lang") === "th")
          {
          return "titleThai";
          }
        else
          {
          return "title";
          }
      },

  },
  created() {
    if (this.loggedIn) {
      this.$router.push("/");
    }
  },
  methods: {
    pageReturn(value) {
      this.page = value;
    },
    forgetCheck() {
      this.page = 2;
    },
    checkInput() {
      this.loading = true;
      if (this.user.email && this.user.password) {
        this.$store.dispatch("auth/login", this.user).then(
          () => {
            this.$router.push("/");
          },
          error => {
            this.loading = false;
            this.message =
              (error.response && error.response.data) ||
              error.message ||
              error.toString();
            alert("Email or password not correct");
          }
        );
      }
    },
    googleOnSuccess(googleUser) {
      var userProfile = googleUser.getBasicProfile();
      this.user.email = userProfile.getEmail();
      this.user.authId = userProfile.getId();
      this.loading = true;
      this.$store.dispatch("auth/login", this.user).then(
        () => {
          this.$router.push("/");
        },
        error => {
          this.loading = false;
          this.message =
            (error.response && error.response.data) ||
            error.message ||
            error.toString();
          alert("User did not register yet");
        }
      );
    },
    googleOnFailure() {
      alert("Google Sign In Failed");
    },
    backMain() {
      window.location.href = "/";
    }
  }
};
</script>

<style scoped>
* {
  z-index: 1;
}
#register-section {
  text-align: center;
  margin-top: 35px;
}
#login-container {
  display: grid;
  grid-template-columns: 50% 50%;
}
#img-user {
  height: 30px;
  margin-top: -5px;
}
#img-lock {
  height: 24px;
  margin-top: -5px;
}
#cpe-logo {
  height: 407px;
}

.img-login {
  width: 350px;
}
.underline {
  margin-top: 10px;
  margin-bottom: 25px;
  height: 1px;
  opacity: 0.7;
  border: none;
  color: #ffffff;
  background-color: #ffffff;
  font-family: "CloudLight";
}
.descript-text {
  letter-spacing: 1.6px;
  font-size: 1.5em;
  font-family: "CloudLight";
}
.two-background {
  background: linear-gradient(90deg, #302e71 50%, #282567 50%);
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2;
}
#or-text {
  text-align: center;
  margin-top: 10px;
  margin-bottom: 10px;
  font-family: "CloudLight";
  font-size: 2em;
  letter-spacing: 1.6px;
  color: #fbdad3;
  opacity: 0.5;
}
#login-page {
  width: 1440px;
  max-width: 1440px;
}
#backToMain {
  font-family: "CloudBold";
  font-size: 2em;
  color: #ffffff;
}

.box {
  margin-top: 15px;
  font-family: "CloudLight";
  display: flex;
  align-items: center;
}

.textDivider {
  width: 100%;
  text-align: center;
  border-bottom: 2px solid #d5d5df;
  line-height: 0.1em;
  margin: 10px 0 20px;
}

input[type="text"] {
  color: white;
  font-size: 2em;
  font-family: "CloudLight";
}

input[type="password"] {
  color: white;
  font-size: 2em;
  font-family: "CloudLight";
}

#loginButton {
  color: #ffffff;
  background-color: #f28093;
  border: none;
  width: 100%;
  padding-top: 7px;
  padding-bottom: 7px;
  font-size: 2.5em;
  letter-spacing: 3px;
  font-family: "CloudBold";
}

input[type="text"]:focus {
  border: none;
  background-color: none;
  outline: 0;
}

input[type="password"]:focus {
  border: none;
  background-color: none;
  outline: 0;
}

::placeholder {
  color: #bbbde4;
  font-size: 0.8em;
  font-family: "CloudLight";
  opacity: 0.6;
}

.input {
  margin-left: 10px;
  padding-left: 10px;
  background: transparent;
  border: none;
  width: 100%;
}
body,
html {
  padding: 0;
  margin: 0;
}

#forgot {
  text-align: right;
  font-size: 2em;
  font-family: "CloudLight";
  color: white;
  margin-bottom: 10px;
  margin-top: 0;
  opacity: 0.7;
}

.title {
  text-align: center;
  padding-top: 50px;
  padding-bottom: 10px;
  font-size: 8em;
  font-family: "CloudBold";
  letter-spacing: 11.6px;
  color: #ffffff;
  opacity: 1;
  margin-bottom: 0px;
  margin-top: 0px;
  z-index: 2;
}

.titleThai {
  text-align: center;
  padding-top: 50px;
  padding-bottom: 10px;
  font-size: 7em;
font-family: "CloudBold";
  letter-spacing: 11.6px;
  color: #ffffff;
  opacity: 1;
  margin-bottom: 0px;
  margin-top: 0px;
  z-index: 2;
}

#left-side {
  display: flex;
  justify-content: center;
  align-items: center;
}

#right-side {
  display: flex;
  justify-content: center;
  align-items: center;
}

@media screen and (max-width: 1024px) {
  .two-background {
    background: #282567;
  }
  #login-container {
    display: block;
  }
  #right-side {
    display: none;
  }
}

@media screen and (max-width: 414px) {
  .title {
    font-size: 6em;
  }
  .underline {
    margin-bottom: 16px;
  }
  #forgot {
    font-size: 1.75em;
    margin-bottom: 5px;
  }
  #backToMain,
  .descript-text,
  #or-text {
    font-size: 1.5em;
  }
  #register-section {
    margin-top: 25px;
  }
  .title {
    font-size: 6em;
  }
  .two-background {
    display: flex;
    justify-content: center;
    align-items: normal;
    padding-top: 25px;
    padding-top: 25px;
    background: #282567;
    height: 100vh;
  }
}
@media screen and (max-width: 375px) {
  .img-login {
    width: 300px;
  }
  #img-user {
    height: 25px;
    margin-top: -1px;
  }
  #img-lock {
    height: 19px;
    margin-top: 0px;
  }
}
@media screen and (max-width: 360px) {
  .img-login {
    width: 290px;
  }
}
</style>
