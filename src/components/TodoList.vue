<template>
  <v-container>
    <v-card>
      <v-container>
        <v-row class="d-flex justify-space-around">
          <v-col>
            <v-radio-group v-model="sortTypeVal" @change="getTaskList()" row>
              <v-radio label="По имени" value="sortByName"></v-radio>
              <v-radio label="По email" value="sortByEmail"></v-radio>
              <v-radio label="По статусу" value="sortByStatus"></v-radio>
            </v-radio-group>
          </v-col>
          <v-col>
              <v-icon class="mt-3" :color="switchSortAsc ? 'success' : 'gray'" large @click="newSortDest">mdi-chevron-up</v-icon>
              <v-icon class="mt-3" :color="switchSortAsc ? 'gray' : 'success'"  large @click="newSortDest">mdi-chevron-down</v-icon>
          </v-col>
          <v-col>
            <AddNewTask v-on:reload-task-list="getTaskList()" />
          </v-col>
        </v-row>
      </v-container>
    </v-card>

    <v-card v-for="record in recordsObject" :key="record.id" class="pa-4 my-3" outlined>
      <single-task v-bind:record="record" /> 
      
    </v-card>

    <!-- pagination -->
    <div class="text-center">
      <v-pagination
        v-show="numberOfPages>1"
        v-model="currentPage"
        :length="numberOfPages"
        prev-icon="mdi-menu-left"
        next-icon="mdi-menu-right" @input="getTaskList()"
      ></v-pagination>
    </div>
  </v-container>
</template>


<script>
import axios from "axios";
import AddNewTask from "./AddNewTask";
import SingleTask from "./SingleTask";

export default {
  data() {
    return {
      recordsObject: "",
      currentPage: 1,
      numberOfPages: 0,
      sortType: ["По имени", "По email", "По статусу"],
      switchSortAsc: false,
      sortTypeVal: 'sortByName',
    };
  },
  mounted() {
    this.getTaskList();
  },
  methods: {
    getTaskList: function() {
      axios({
        method: "post",
        url: "http://other/TODOApi/api/product/read.php",
        crossDomain: true,
        data: JSON.stringify({
        sortAsc: this.switchSortAsc,
        sortType: this.sortTypeVal,
        page: this.currentPage,
        itemsOnPage: 3

      }),
        
      })
        .then(response => {
          this.recordsObject = response.data.records;
          let numberOfRecords = Number(response.data.count["count"]);
          this.numberOfPages = Math.ceil(numberOfRecords / 3);

          /* eslint-disable no-console */
          console.log(this.recordsObject);
          /* eslint-enable no-console */
        })
        .catch(function(error) {
          /* eslint-disable no-console */
          console.log(error);
          /* eslint-enable no-console */
        });
    },
    newSortDest: function(){
      this.switchSortAsc = !this.switchSortAsc;

      this.getTaskList();

    }
  },
  computed: {

  },

  components: {
    AddNewTask,
    SingleTask,
  }
};
</script>

