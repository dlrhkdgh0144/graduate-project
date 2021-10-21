<template>
  <v-app>
    <v-row>
      <h2 style="margin-top: 20px">데이터 공유</h2>
    </v-row>
    <v-row>
      <h3>제목</h3><br>
      <input v-model="formData.title"
             placeholder="제목"
             class="title"
      >
    </v-row>
    <v-row>
      <h3>설명</h3><br>
      <input v-model="formData.description"
             placeholder="내용 설명"
             class="description"
      >
    </v-row>
    <v-row>
      <h3>파일 업로드</h3>
      <v-file-input
        truncate-length="15"
        label="파일선택" color="blue"
        @change="onFileSelect"
      />
      <v-btn>업로드</v-btn>
      <v-btn @click="uploadPost"
      >게시</v-btn>
    </v-row>
  </v-app>
</template>

<script>

export default {
  name: "post",
  data: () => ({
    formData: {
      title: '',
      filename: '',
      description: '',
      writer: '',
      timestamp: '',
    },
    fileObj : null,
  }),
  mounted() {
    if(!this.$fire.auth.currentUser){
      this.$router.push('/login');
    }
    this.formData.writer = this.$fire.auth.currentUser.displayName;
  },
  methods: {
    onFileSelect(file){
      this.fileObj = file;
    },
    async uploadPost(){
      this.formData.timestamp = this.$fireModule.firestore.FieldValue.serverTimestamp();

      // const AWS = require('aws-sdk');
      // const fs = require('fs');
      // const s3 = new AWS.S3({
      //   accessKeyId : process.env.ACCESS_KEY,
      //   secretAccessKey : process.env.SECRET_KEY
      // });
      // const uploadFile = (fileName)=>{
      //   const fileContent = fs.readFileSync(fileName);
      //   const param = {
      //     'Bucket':'graduate-project-lgh',
      //     'Key': this.formData.filename,//filename you want to save in s3
      //     'Body':fileContent,//absolute path in local
      //   };
      //   s3.upload(param, function(err, data){
      //     if(err) {
      //       console.log(err);
      //     }
      //     console.log(`File upload success +${data.Location}`);
      //   });
      // }

      //add post to firebase
      await this.$fire.firestore.collection(`posts`).
      add(this.formData);

      await this.$router.push('/board');
    }
  },
}
</script>

<style scoped>
.title{
  width: 80%;
  height: 15%;
  margin: 20px;
  border: black 1px solid;
  border-radius: 5px;
}
.description{
  width: 80%;
  height: 70%;
  margin: 10px;
  border: black 1px solid;
  border-radius: 5px;
}
</style>
