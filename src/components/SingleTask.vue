<template>
  <div>
   <!--  <p v-for="(recordfields, index) in record" :key="index">{{ index }} : {{recordfields}}</p> -->
    <div class="overline mb-2">От {{ record.name }} ({{ record.email }})</div>
    <v-card-title>
      {{ record.description }}
      <v-list-item-avatar tile size="50" class="ml-auto">
          <v-icon large color="success">
              mdi-check
          </v-icon>
      </v-list-item-avatar>
    </v-card-title>
  </div>
</template>

<script>
export default {
  props: ["record"],
  data() {
    return {};
  }
};
</script>
