<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-btn
        icon
        @click.stop="miniVariant = !miniVariant"
      >
        <v-icon>mdi-{{ `chevron-${miniVariant ? 'right' : 'left'}` }}</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="clipped = !clipped"
      >
        <v-icon>mdi-application</v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="fixed = !fixed"
      >
        <v-icon>mdi-minus</v-icon>
      </v-btn>
      <v-spacer/>
      <v-btn
        icon
        @click="$router.push('/')"
      >
        <v-icon>mdi-home</v-icon>
        Home
      </v-btn>
      <v-spacer/>
      <v-btn
        icon
        @click="$router.push('/board')"
      >
        <v-icon>mdi-clipboard-outline</v-icon>
        Board
      </v-btn>
      <v-spacer/>
      <div v-if="!isLoggedIn" onsubmit="return false;">
        <v-btn
          color="primary"
          to="/login"
        >
          로그인
        </v-btn>
      </div>
      <div v-else>
        <span>{{authUser.nickname}}</span>
        <v-btn color="primary" outlined @click="logout">Logout</v-btn>
      </div>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
<!--    <v-navigation-drawer-->
<!--      v-model="rightDrawer"-->
<!--      :right="right"-->
<!--      temporary-->
<!--      fixed-->
<!--    >-->
<!--      <v-list>-->
<!--        <v-list-item @click.native="right = !right">-->
<!--          <v-list-item-action>-->
<!--            <v-icon light>-->
<!--              mdi-repeat-->
<!--            </v-icon>-->
<!--          </v-list-item-action>-->
<!--          <v-list-item-title>Switch drawer (click me)</v-list-item-title>-->
<!--        </v-list-item>-->
<!--      </v-list>-->
<!--    </v-navigation-drawer>-->
    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import {mapGetters, mapState} from 'vuex'
export default {
  computed: {
    ...mapState({
      authUser: state => state.authUser,
    }),
    ...mapGetters({
      isLoggedIn: 'isLoggedIn',
    }),
  },
  data () {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-home',
          title: 'Home',
          to: '/'
        },
        {
          icon: 'mdi-clipboard-outline',
          title: 'Board',
          to: '/board'
        },
        {
          icon: 'mdi-account',
          title: 'Login',
          to: '/login'
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  },
  methods:{
    async logout() {
      try {
        await this.$fire.auth.signOut()
      } catch (e) {
        alert(e)
      }
    },
  },
}
</script>
