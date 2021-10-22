<template>
  <v-dialog
    v-model="isPostviewed"
    @click:outside="closePost"
    @close="closePost"
  >
    <v-card>
      <div>{{ this.postDetail }}</div><br>
      <div>{{this.postTitle}}</div>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  data: ()=>({
    postTitle: '',
    selectedDoc: '',
  }),
  props: [
    'isPostviewed',
    'postDetail',
  ],
  methods: {
    async queryPost(postid){
      await this.$fire.firestore.collection(`posts`).doc(postid).get().then(docSnap => {
        this.postTitle = docSnap.data().title;
        console.log('Title is '+this.postTitle);
      });
    },
    closePost: function (){
      this.isPostviewed = false;
    },
  },
}
</script>

<style scoped>

</style>
