<template>
  <div>
    <div v-for="(res, r) in results" :key="r">
      <div v-if="res.media_type == 'tv'">
        {{ res.name + ", " + res.media_type }}
      </div>
      <div v-else>
        {{ res.title + ", " + res.media_type }}
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "BaseComponent",
  props: {},
  data() {
    return {
      totalPages: null,
      page: 1,
      results: [],
      list: "8242534",
    };
  },
  mounted() {
    this.sendGetRequest();
  },
  computed: {},
  methods: {
    async sendGetRequest() {
      let data = {
        api_key: "94c585ad2dedd467999c78da549f63e9",
        page: this.page,
      };
      axios
        .get("https://api.themoviedb.org/4/list/" + this.list, {
          params: data,
        })
        .then((response) => {
          // Handle the response

          this.totalPages = response.data.total_pages;
          let results = response.data.results;
          results.forEach((element) => {
            if (element.media_type == "movie") this.results.push(element);
          });
          // Check if there are more pages
          if (this.page < this.totalPages) {
            this.page += 1;
            this.sendGetRequest(this.page); // Make request for the next page
          } else {
            console.log("All pages processed.");
          }
        })
        .catch((error) => {
          // Handle the error
          console.error(error);
        });
      console.log(this.results);
    },

    //   try {
    //     const resp = await axios.get(
    //       "https://api.themoviedb.org/4/list/8242534",
    //       {
    //         params: data,
    //       }
    //     );
    //   } catch (err) {
    //     // Handle Error Here
    //     console.error(err);
    //   }
    // },
  },
};
</script>

<style>
/* Estilos CSS */
</style>
