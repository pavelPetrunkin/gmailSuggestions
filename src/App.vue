<template>
  <v-app>
    <v-main>
      <GmailMessage />
    </v-main>
  </v-app>
</template>

<script>
import GmailMessage from './components/GmailMessage';

export default {
  name: 'App',

  components: {
    GmailMessage,
  },

  data: () => ({
    //
  }),
};
</script>
