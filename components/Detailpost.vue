<template>
  <v-dialog
    v-model="isPostviewed"
    @click:outside="closePost"
    @close="closePost"
    max-width="1000"
  >
    <v-card>
      <v-toolbar
        color="primary"
        dark>
        <v-toolbar-title>Post Detail</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn icon @click="closePost">
          <v-icon>mdi-close</v-icon>
        </v-btn>
      </v-toolbar>
      <div style="padding: 30px">
        <h2>{{ this.postTitle }}</h2><br>
        <h3>작성자 : {{this.postWriter}}</h3><br>
        <h3>작성일시 : {{this.postTime}}</h3><br>
        <hr style="margin-bottom: 20px">
        <div class="descript">{{this.postDescription}}</div><br>
        <div>파일명 : {{this.postFile}}</div>
        <v-btn @click="downloadFile"
               id="downloadBtn">
          <v-icon>mdi-content-save</v-icon>
          다운로드
        </v-btn>

      </div>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  data: ()=>({
    postTitle: '',
    postDescription: '',
    postWriter: '',
    postTime: '',
    postFile: '',
    fileUrls: '',
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
        this.postDescription = docSnap.data().description;
        this.postWriter = docSnap.data().writer;
        this.postTime = this.convertTime(docSnap.data().timestamp);
        this.postFile = docSnap.data().filename;
        let filepath = docSnap.data().path ? this.$fire.storage.ref(docSnap.data().path).getDownloadURL() : '';
        filepath.then(url=>{
          console.log(url);
          this.fileUrls = url;
        })
      });
    },
    closePost: function (){
      this.isPostviewed = false;
    },
    convertTime(tstamp){
      let date = new Date(tstamp*1000);
      let result = (date.getFullYear()-1969)+'.'+(date.getMonth()+1)+'.'+date.getDate()+' '
      +String(date.getHours()).padStart(2,"0")+':'+String(date.getMinutes()).padStart(2,"0");

      return result;
    },
    downloadFile(){
      // let xhr = new XMLHttpRequest();
      // xhr.responseType = 'blob';
      // xhr.onload = function(event) {
      //   let blob = xhr.response;
      // };
      // xhr.open('GET', this.fileUrls);
      // xhr.send();

      const axios = require('axios');
      axios({
        url: this.fileUrls,
        method: 'GET',
        responseType: 'blob'
      })
        .then((response) => {
          const url = window.URL
            .createObjectURL(new Blob([response.data]));
          const link = document.createElement('a');
          link.href = url;
          link.setAttribute('download', this.postFile);
          document.body.appendChild(link);
          link.click();
          document.body.removeChild(link);
        })
    },
  },
}
</script>

<style scoped>
.descript{
  height: 100%;
  margin-bottom: 30px;
}
</style>
