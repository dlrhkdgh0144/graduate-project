<template>
  <v-app justify="center">
    <v-row>
      <h2 style="margin-top: 20px">데이터 목록</h2>
      <v-spacer></v-spacer>
      <input placeholder="제목"
             class="search-bar"
      >
      <v-btn class="search-btn"
      ><v-icon left>mdi-magnify</v-icon>
        검색
      </v-btn>
    </v-row>
    <v-row>
      <v-simple-table class="post-list">
        <tr style="text-align: left">
          <th>번호</th>
          <th>제목</th>
          <th>작성자</th>
          <th>날짜</th>
        </tr>
        <tbody>
          <tr v-for="(posts,index) in postlist">
            <td>{{postlist.length-index}}</td>
            <td>{{ posts.data().title }}</td>
            <td>{{ posts.data().writer }}</td>
            <td>{{ convertTime(posts.data().timestamp) }}</td>
          </tr>
        </tbody>
      </v-simple-table>

    </v-row>
    <v-row>
      <v-btn @click="posting"
      >작성</v-btn>
    </v-row>
  </v-app>
</template>

<script>
import {mapGetters, mapState} from "vuex";
import firebase from "firebase";

export default {
  name: "board",
  data: () => ({
    postlist : [],
  }),
  computed: {
    ...mapState({
      authUser: state => state.authUser,
    }),
    ...mapGetters({
      isLoggedIn: 'isLoggedIn',
    }),
  },
  created() {
    this.$fire.firestore.
    collection(`posts`).orderBy('timestamp',"desc").onSnapshot((async querySnapshot => {
      console.log(querySnapshot.docs.length);
      this.postlist = querySnapshot.docs;
      console.log(this.postlist[0]);
    }));
  },
  methods : {
    posting() {
      if(this.isLoggedIn){
        this.$router.push('/post');
      }
      else{
        this.$router.push('/login');
      }
    },
    convertTime(tstamp){
      let date = new Date(tstamp*1000);
      console.log('Time stamp is '+date);
      let result = (date.getFullYear()-1969)+'.'+(date.getMonth()+1)+'.'+date.getDate();
      return result;
    }
  }
}
</script>

<style scoped>
.search-bar{
  border: black 1px;
  margin-top: 20px;
  height: 30px;
}
.search-btn{
  margin-top: 17px;
  width: 10px;
}
.post-list{
  width: 90%;
}
</style>
