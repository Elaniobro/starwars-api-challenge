<template>
  <v-footer
    dark
    height="auto"
    fixed
  >
    <v-card
      class="flex"
      flat
      tile
      color="#000"
    >
      <v-card-title>
        <strong class="subheading">Cyber stalk the developer and hire them!</strong>
        <v-spacer></v-spacer>
        <v-btn
          v-for="icon in icons"
          :key="icon.id"
          dark
          icon
        >
          <a :href="icon.url" target="_blank"><v-icon size="24px">{{ icon.name }}</v-icon></a>
        </v-btn>
      </v-card-title>
      <v-card-actions class="black darken-3 justify-center">
        &copy;2018 — <strong>Elan Trybuch</strong>
      </v-card-actions>
    </v-card>
  </v-footer>
</template>

<style scoped>
  a:link,
  a:hover {
    color: #fff;
    text-decoration: none;
  }
</style>

<script>
export default {
  data: () => ({
    icons: [
      {
        id: 0,
        name: 'fab fa-twitter',
        url: 'https://www.twitter.com/elaniobro',
      },
      {
        id: 1,
        name: 'fab fa-github',
        url: 'https://www.github.com/elaniobro',
      },
      {
        id: 2,
        name: 'fab fa-linkedin',
        url: 'https://www.linkedin.com/in/elantrybuch/',
      },
      {
        id: 3,
        name: 'fab fa-instagram',
        url: 'https://www.instagram.com/elaniobro',
      },
    ],
  }),
};
</script>
