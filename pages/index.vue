<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <div class="text-center"
      style="margin-top: 50px">
        <logo/>
        <vuetify-logo/>
        <h1 style="margin: 40px">Web platform for IoT data sharing</h1>
      </div>
    </v-col>
  </v-row>
</template>

<script>
import Logo from '~/components/Logo.vue';
import VuetifyLogo from '~/components/VuetifyLogo.vue';

export default {
  components: {
    Logo,
    VuetifyLogo,
  },
};
</script>
