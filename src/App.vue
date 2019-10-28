<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>TODO</span>
        <span class="font-weight-light">BeeJee</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <span
        v-show="helloUser"
        class="light-blue--text subtitle-2 text--darken-4 mr-4"
        border="right"
      >{{ helloUser }}</span>
      <v-divider v-show="helloUser" class="mx-2" inset vertical></v-divider>
      <v-btn text v-if="userNotLogin" @click.stop="userLogin">
        <v-icon>mdi-login</v-icon>
        <span class="mr-2">Login</span>
      </v-btn>
      <v-btn text v-else @click.stop="userLogout">
        <span class="mr-2">Logout</span>
        <v-icon>mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content>
      <!-- login modal -->
      <v-row justify="center">
        <v-dialog v-model="dialog" max-width="290">
          <v-card class="pt-1">
            <v-card-title class="mx-auto">
              <span class="headline">Авторизация</span>
            </v-card-title>
            <v-col cols="12">
              <v-text-field
                label="Имя пользователя*"
                counter="20"
                outlined
                dense
                v-model="user.login"
                required
              ></v-text-field>
              <span
                class="red--text text--darken-3"
                v-show="!this.validate.login"
              >Введите корректный логин</span>
            </v-col>
            <v-col cols="12">
              <v-text-field
                label="Пароль*"
                counter="30"
                type="password"
                outlined
                dense
                v-model="user.password"
                required
              ></v-text-field>
              <span
                class="red--text text--darken-3"
                v-show="!this.validate.password"
              >Введите корректный пароль</span>
            </v-col>
            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn color="green darken-1" text @click="dialog = false">Отмена</v-btn>

              <v-btn color="green darken-1" text @click="validateLogin">Войти</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-row>
      <!-- end login modal -->

      <TodoList v-bind:userrole="administrator" />
    </v-content>
  </v-app>
</template>

<script>
import TodoList from "./components/TodoList";
import axios from "axios";

export default {
  name: "App",
  components: {
    TodoList
  },
  data: function() {
    return {
      userNotLogin: true,
      dialog: false,
      user: {
        login: "",
        password: ""
      },
      validate: {
        login: "",
        password: ""
      },
      helloUser: "",
      administrator: false
    };
  },
  methods: {
    validateLogin: function() {
      this.validate.login = /[a-zA-Z0-9]{4,10}/.test(this.user.login);
      this.validate.password = /[a-zA-Z0-9]{3,10}/.test(this.user.password);
      if (this.validate.login && this.validate.password) this.sendUserData();
    },
    sendUserData: function() {
      axios({
        method: "post",
        url: "https://astred.ru/api/login/login.php",
        crossDomain: true,
        data: JSON.stringify({
          userName: this.user.login,
          userPassword: this.user.password
        })
      })
        .then(response => {
          if (response.data == "Unknown_user") {
            this.administrator = false;
          } else if (response.data == "Administrator") {
            this.administrator = true;
          }
          this.setUserDataToCookie();
          this.getUserDataFromCookie();
          this.userNotLogin = false;
          this.helloUser = "Привет, " + this.user.login;
          this.dialog = false;
        })
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
          /* eslint-enable no-console */
        });
    },
    setUserDataToCookie: function() {
      document.cookie = "user=" + encodeURIComponent(this.user.login);
      document.cookie = "password=" + encodeURIComponent(this.user.password);
      document.cookie =
        "administrator=" + encodeURIComponent(this.administrator);
    },
    getUserDataFromCookie: function() {},
    userLogout: function() {
      this.user.login = "";
      this.user.password = "";
      this.userNotLogin = true;
      this.helloUser = "";
      this.administrator = false;
      localStorage.setItem("admin", false);
      sessionStorage.setItem("nologin", true);
    },
    userLogin: function() {
      this.dialog = true;
      this.user.login = getCookie("user");
      this.user.password = getCookie("password");
      localStorage.setItem("admin", true);
    }
  },
  mounted: function() {
    if (!sessionStorage.getItem("nologin")) {
      this.user.login = getCookie("user");
      this.user.password = getCookie("password");
      this.sendUserData();
    }
  }
};

function getCookie(name) {
  var cookie = " " + document.cookie;
  var search = " " + name + "=";
  var setStr = null;
  var offset = 0;
  var end = 0;
  if (cookie.length > 0) {
    offset = cookie.indexOf(search);
    if (offset != -1) {
      offset += search.length;
      end = cookie.indexOf(";", offset);
      if (end == -1) {
        end = cookie.length;
      }
      setStr = unescape(cookie.substring(offset, end));
    }
  }
  return setStr;
}
</script>
