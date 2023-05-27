<template>
  <div>
    <select name="pets" id="pet-select" @change="irA()" v-model="inputId">
      <option value="">Elija una peliculapara ir directamente</option>
      <option v-for="(res, r) in results" :key="r" :value="res.id">
        {{ res.original_title }}
      </option>
    </select>
    <button @click="irA()">asdlfpkl</button>
    <button @click="ordenarFecha()">orden fecha</button>
    <button @click="ordenarPop()">orden pop</button>
    <draggable
      v-model="results"
      group="movies"
      @start="drag = true"
      @end="
        drag = false;
        hola();
      "
      item-key="id"
    >
      <template #item="{ element }">
        <div class="container" :id="element.id">
          <img
            :src="'https://image.tmdb.org/t/p/w1280/' + element.backdrop_path"
            alt=""
            srcset=""
          />
          <div class="item" ref="item" @click="expand = !expand">
            <div
              style="
                background: #22223b;
                height: fit-content;
                display: flex;
                align-items: start;
                justify-content: flex-start;
              "
            >
              <span style="font-size: 18px">
                {{
                  formatDate(
                    element.original_title,
                    element.release_date,
                    element.vote_average
                  )
                }}
              </span>
            </div>
          </div>
        </div>
      </template>
    </draggable>
  </div>
</template>

<script>
import axios from "axios";
import draggable from "vuedraggable";

export default {
  name: "BaseComponent",
  components: {
    draggable,
  },
  props: {},
  data() {
    return {
      expand: false,
      drag: false,
      totalPages: null,
      page: 1,
      results: [],
      randomArray: [],
      list: "8242534",
      inputId: null,
    };
  },
  mounted() {
    this.sendGetRequest();
  },
  methods: {
    hola() {
      console.log(this.results);
    },
    irA() {
      console.log(this.inputId);
      var access = document.getElementById(this.inputId);
      console.log(access);
      access.scrollIntoView({ behavior: "smooth" }, true);
    },
    ordenarPop() {
      this.results.sort(function (a, b) {
        var keyA = a.vote_average,
          keyB = b.vote_average;
        // Compare the 2 dates
        if (keyA < keyB) return 1;
        if (keyA > keyB) return -1;
        return 0;
      });
      console.log(this.results);
    },
    ordenarFecha() {
      this.results.sort(function (a, b) {
        var keyA = new Date(a.release_date),
          keyB = new Date(b.release_date);
        // Compare the 2 dates
        if (keyA < keyB) return 1;
        if (keyA > keyB) return -1;
        return 0;
      });
      console.log(this.results);
    },
    async sendGetRequest() {
      let data = {
        api_key: "94c585ad2dedd467999c78da549f63e9",
        page: this.page,
      };
      await axios
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
          }
        })
        .catch((error) => {
          // Handle the error
          console.error(error);
        });
      console.log(this.results);
    },
    randomSelect() {
      for (let index = 0; index < 2; index++) {
        const element = Math.floor(Math.random() * this.results.length);
        this.randomArray.push(this.results[element]);
      }
      console.log(this.randomArray);
    },
    formatDate(peli, fecha, vote) {
      const date = new Date(fecha);
      const year = date.getFullYear();
      console.log(year); // 2010
      return peli + ", " + year + ", " + vote;
    },
    ver() {
      this.$refs.item.classList.value = "hola";
      console.log(this.$refs.item.classList);
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
.expanded {
  position: relative;
  overflow: hidden;
  margin: 10px 0px;
  transition: ease-in-out 0.2s;
}

.container {
  width: 1260px;
  position: relative;
  height: 100px;
  overflow: hidden;
  margin: 10px 0px;
}

.item {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  transition: ease-in-out 0.2s;
}

.item:hover {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0);
}

.container img {
  width: 100%;
  height: auto;
  object-fit: cover;
}
</style>
