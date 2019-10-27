<template>
  <v-row justify="center">
    <v-dialog v-model="dialog" max-width="600px">
      <template v-slot:activator="{ on }">
        <v-btn dark color="red darken-3" tile class="mt-3" v-on="on">Добавить задачу</v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Добавление задачи</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field label="Имя" v-model="newTask.name" required></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-text-field label="Email" v-model="newTask.email" required></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field label="Задача" v-model="newTask.description" required></v-text-field>
              </v-col>
            </v-row>
          </v-container>
          <small>Все поля обязательны для заполнения</small>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false">Закрыть</v-btn>
          <v-btn color="blue darken-1" text @click="saveTask">Сохранить</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import axios from "axios";

export default {
  data: () => ({
    dialog: false,
    newTask: {
      name: "",
      email: "",
      description: ""
    }
  }),
  methods: {
    saveTask: function() {


      axios({
        method: "post",
        data: JSON.stringify({
        name: this.newTask.name,
        email: this.newTask.email,
        description: this.newTask.description,
        created: new Date()
      }),
        url: "http://other/TODOApi/api/product/create.php",
        crossDomain: true
      })
        .then(response => {
          this.recordsObject = response.data.records;
          
          
          // Очистка данных формы
          var props = Object.keys(this.newTask);
          for (var i = 0; i < props.length; i++) {
            delete this.newTask[props[i]];
          }

          this.dialog = false;
          this.$emit('reload-task-list');
          /* eslint-disable no-console */
          console.log(response);
          console.log("yes");

          /* eslint-enable no-console */
        })
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log("no");
          console.log(error);
          /* eslint-enable no-console */
        });
    }
  }
};
</script>