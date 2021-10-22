<template>
  <v-dialog
    v-model="isPostviewed"
    @click:outside="closePost"
    @close="closePost"
  >
    <v-card>
      <div>Hi</div>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: "Detailpost",
  data: ()=>({
    postTitle: '',
  }),
  props: [
    'isPostviewed',
  ],
  methods: {
    closePost: function (){
      this.isPostviewed = false;
    },
  },
}
</script>

<style scoped>

</style>
