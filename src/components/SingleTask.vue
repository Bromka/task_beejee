<template>
  <div>
    <!-- <p v-for="(recordfields, index) in record" :key="index">{{ index }} : {{recordfields}}</p> -->
    <div class="overline mb-2">От {{ record.name }} ({{ record.email }})</div>
    <v-card-title>
      {{ record.description }}
      <v-list-item-avatar tile size="50" class="ml-auto">
        <v-icon v-show="record.taskdone" large color="success">mdi-check</v-icon>
      </v-list-item-avatar>
    </v-card-title>
    <v-divider></v-divider>
    <span class="caption text--gray lighten-4 font-italic font-weight-light">{{ taskHasEdited }}</span>

    <v-dialog v-model="dialog" max-width="600px">
      <template v-slot:activator="{ on }">
        <v-btn text icon color="gray" v-on="on" v-show="userrole">
          <v-icon @click="itIsAdmin">mdi-pencil</v-icon>
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Редактирование задачи</span>
        </v-card-title>
        <v-card-text v-if="isAdmin">
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field label="Задача" v-model.lazy="taskDescription" required></v-text-field>
                <v-switch v-model="taskDone" label="Выполнено"></v-switch>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-text v-else>
          <v-container>
            <v-row>
              <v-col cols="12">
                <p>Необходимы права администратора</p>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false">Закрыть</v-btn>
          <v-btn color="blue darken-1" text @click="editTask">Сохранить</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: ["record", "userrole"],
  data() {
    return {
      message: "Отредактированно администратором",
      dialog: false,
      taskDescription: this.record.description,
      taskDone: this.record.taskdone,
      isAdmin: ""
    };
  },
  computed: {
    taskHasEdited: function() {
      if (this.record.modifed !== this.record.created) {
        return this.message;
      } else return null;
    }
  },
  methods: {
    editTask: function() {
      axios({
        method: "post",
        url: "https://astred.ru/api/product/edit.php",
        crossDomain: true,
        data: JSON.stringify({
          taskID: this.record.id,
          taskDescription: this.taskDescription,
          taskDone: this.taskDone
        })
      })
        .then(response => {
          this.dialog = false;
          this.$emit("reload-task-list");
          /* eslint-disable no-console */
          console.log(response);
          /* eslint-enable no-console */
        })
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
          /* eslint-enable no-console */
        });
    },
    itIsAdmin: function() {
      if (this.userrole && localStorage.getItem('admin')) {
        
        
        this.isAdmin = true;
      } else this.isAdmin = false;
    }
  }
};

</script>
