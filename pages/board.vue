<template>
  <v-app justify="center">
    <v-row style="height: 100px">
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
        <thread>
          <tr>
            <th>번호</th>
            <th>제목</th>
            <th>작성자</th>
            <th>다운로드</th>
          </tr>
        </thread>
        <tbody>
          <tr>
            <td>1</td>
            <td>Terraforming Mars</td>
            <td>Jacob Fryxelius</td>
            <td>ㅇㅇ</td>
          </tr>
          <tr>
            <td>2</td>
            <td>Gloomhaven: Jaws of the Lion</td>
            <td>Uve</td>
            <td>down</td>
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
  computed: {
    ...mapState({
      authUser: state => state.authUser,
    }),
    ...mapGetters({
      isLoggedIn: 'isLoggedIn',
    }),
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
  width: 100%;
}
</style>
