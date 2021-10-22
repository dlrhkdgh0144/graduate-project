<template>
  <v-app>
    <v-row>
      <h2 style="margin-top: 40px">데이터 공유</h2>
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
    </v-row>
    <v-row>
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
      path: '',
      timestamp: '',
    },
    fileObj : null,
    isUploading : false,
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
      this.formData.filename = this.fileObj.name;
      console.log(this.fileObj);
    },
    async uploadPost(){
      this.formData.timestamp = this.$fireModule.firestore.FieldValue.serverTimestamp();

      // const AWS = require('aws-sdk');
      // //const fs = require('fs');
      // const s3 = new AWS.S3({
      //   accessKeyId : process.env.ACCESS_KEY,
      //   secretAccessKey : process.env.SECRET_KEY
      // });
      //
      // const fileContent = this.fileObj;
      // const param = {
      //   'Bucket':'graduate-project-lgh',
      //   'Key': this.formData.filename,//filename you want to save in s3
      //   'ACL': 'public-read',
      //   'Body':fileContent,//absolute path in local
      // };
      // console.log('Request Parameter ');
      // console.log(param);
      // await s3.upload(param, function(err, data){
      //   if(err) {
      //     console.log(err);
      //   }
      //   console.log(`File upload success +${data.Location}`);
      // });

      if(!this.fileObj) {
        return alert('파일을 선택해주세요.');
      }

      const storageRef = this.$fire.storage.ref(`files/${this.fileObj.name}`);
      const uploadTask = storageRef.put(this.fileObj);
      this.isUploading = true;
      const self = this;

      uploadTask.on('state_changed', async function(snapshot){
        // Observe state change events such as progress, pause, and resume
        // Get task progress, including the number of bytes uploaded and the total number of bytes to be uploaded
        const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;

        console.log('Upload is ' + progress + '% done');
        switch (snapshot.state) {
          case self.$fireModule.storage.TaskState.PAUSED: // or 'paused'
            console.log('Upload is paused');
            break;
          case self.$fireModule.storage.TaskState.RUNNING: // or 'running'
            console.log('Upload is running');
            break;
        }
      }, function(error) {
        // Handle unsuccessful uploads
        alert('error');
        this.isUploading = false;
      }, async function() {
        console.log('success!');
        // Handle successful uploads on complete
        // For instance, get the download URL: https://firebasestorage.googleapis.com/...
        console.log(uploadTask.snapshot.ref.fullPath);

        self.formData.path = uploadTask.snapshot.ref.fullPath;

        //add post to firebase
        await self.$fire.firestore.collection(`posts`).
        add(self.formData);

        this.isUploading = false;

        uploadTask.snapshot.ref.getDownloadURL().then(function(downloadURL) {
          console.log('File available at', downloadURL);
        });
      });

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
