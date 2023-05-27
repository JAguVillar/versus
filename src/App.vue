<template>
  <div>
    <div class="bg-gray-300 w-100 sticky h-fit p-3 flex justify-between">
      <select @change="irA()" v-model="inputId">
        <option value="" disabled selected>
          Elija una peliculapara ir directamente
        </option>
        <option v-for="(res, r) in myArray" :key="r" :value="res.id">
          {{ res.original_title }}
        </option>
      </select>

      <div>
        <button class="mx-3" @click="miLista()">Mi lista</button>
        <button class="mx-3" @click="ordenarFecha()">
          Ordenar por lanzamiento
        </button>
        <button class="mx-3" @click="ordenarPop()">
          Ordenar por valoración
        </button>
        <button class="mx-3" @click="comparar()">Comparar</button>
      </div>
    </div>
    <div :class="compare == true ? 'flex' : ''">
      <div v-if="mostrar == 'myArray' || compare == true" class="m-auto">
        <draggable
          v-model="myArray"
          group="movies"
          @start="drag = true"
          @end="drag = false"
          item-key="id"
        >
          <template #item="{ element }">
            <div
              @click="element.expand = !element.expand"
              :class="element.expand == false ? 'container' : 'expanded'"
              :id="element.id"
            >
              <img
                :src="'https://image.tmdb.org/t/p/w500/' + element.poster_path"
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
                  <span style="font-size: 18px; color: white">
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
      <div v-if="mostrar == 'results' || compare == true" class="m-auto">
        <draggable
          v-model="results"
          group="movies"
          @start="drag = true"
          @end="drag = false"
          item-key="id"
        >
          <template #item="{ element }">
            <div class="container" :id="element.id">
              <img
                :src="
                  'https://image.tmdb.org/t/p/w1280/' + element.backdrop_path
                "
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
                  <span style="font-size: 18px; color: white">
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
    </div>
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
      myArray: [],
      randomArray: [],
      list: "8242534",
      inputId: null,
      mostrar: "myArray",
      compare: false,
    };
  },
  mounted() {
    this.sendGetRequest();
  },
  methods: {
    comparar() {
      this.compare = !this.compare;
      console.log(this.compare);
    },
    miLista() {
      this.mostrar = "myArray";
      console.log(this.mostrar);
      console.log(this.myArray);
    },
    irA() {
      var access = document.getElementById(this.inputId);
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
      this.mostrar = "results";
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
      this.mostrar = "results";
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
            element.expand = false;
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
        });
      this.myArray = this.results;
    },
    randomSelect() {
      for (let index = 0; index < 2; index++) {
        const element = Math.floor(Math.random() * this.results.length);
        this.randomArray.push(this.results[element]);
      }
    },
    formatDate(peli, fecha, vote) {
      const date = new Date(fecha);
      const year = date.getFullYear();

      return peli + ", " + year + " - Valuación promedio: " + vote;
    },
    ver() {
      this.$refs.item.classList.value = "hola";
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
    //
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
  margin: 10px auto 0px auto;
  transition: ease-in-out 0.2s;
  width: 500px;
}

.container {
  width: 500px;
  position: relative;
  height: 100px;
  overflow: hidden;
  margin: 10px auto 0px auto;
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

  object-fit: cover;
}
</style>
