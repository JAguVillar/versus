<template>
  <div></div>
</template>

<script>
import axios from "axios";

export default {
  name: "BaseComponent",
  props: {},
  data() {
    return {};
  },
  mounted() {
    this.sendGetRequest();
  },
  computed: {},
  methods: {
    async sendGetRequest() {
      try {
        const resp = await axios.get(
          "https://api.themoviedb.org/4/list/8242534?api_key=94c585ad2dedd467999c78da549f63e9"
        );
        console.log(resp.data.results);
      } catch (err) {
        // Handle Error Here
        console.error(err);
      }
    },
  },
};
</script>

<style>
/* Estilos CSS */
</style>
