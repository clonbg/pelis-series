<template>
  <q-page class="margen">
    <q-input
      v-model="busqueda"
      type="text"
      label="Busqueda"
      value="pelis"
      @change="loadData"
      @click="busqueda=null"
      class="claseInput"
    />
    <div class="row">
      <div class="col-12">
        <q-btn color="blue" class="full-width" label="Buscar" @click="loadData" />
      </div>
    </div>

    <div class="row">
      <div class="col-6">
        <q-btn
          color="green"
          class="full-width"
          label="Peliculas"
          :disable="elige=='pelis'"
          @click="buscaPelis"
        />
      </div>
      <div class="col-6">
        <q-btn
          color="green"
          class="full-width"
          label="Series"
          :disable="elige=='series'"
          @click="buscaSeries"
        />
      </div>
    </div>
    <div class="row q-gutter-xs">
      <q-card class="my-card tarjeta col-6" v-for="item in arrayMostrar" :key="item.imbdID">
        <img :src="'https://image.tmdb.org/t/p/original'+item.poster_path" class="poster" />

        <q-card-section>
          <div v-if="elige=='series'" class="titulo">{{item.name}}</div>
          <div v-else class="titulo">{{item.title}}</div>
          <!-- fecha -->
          <div class="anio">{{item.release_date}}</div>
        </q-card-section>
      </q-card>
      {{arrayMostrar}}
    </div>
  </q-page>
</template>

<script>
import Vue from "vue";
import axios from "axios";
import AxiosPlugin from "vue-axios-cors";

Vue.use(AxiosPlugin);
export default {
  name: "PageIndex",
  data() {
    return {
      busqueda: null,
      arrayMostrar: [],
      elige: "pelis"
    };
  },
  methods: {
    loadData() {
      var strElige
      if (this.elige=='pelis'){
        strElige='movie'
      } else if (this.elige=='series') {
        strElige='tv'
      }
      this.$axios
        .get(
          "https://api.themoviedb.org/3/search/"+strElige+"?api_key=e1709d76aee13987e8728624138465aa&language=es-ES&query=" +
            String(this.busqueda)
        )
        .then(response => {
          console.log(response.data.results);
          this.arrayMostrar = [];
          response.data.results.forEach(element => {
            if (element.poster_path != null) {
              this.arrayMostrar.push(element);
            }
          });
        })
        .catch(e => {
          console.log(e);
        });
      this.arrayMostrar.sort(function(a, b) {
        //ordenar
        if (a.popularity > b.popularity) {
          return 1;
        }
        if (a.popularity < b.popularity) {
          return -1;
        }
        // a must be equal to b
        return 0;
      });
    },
    buscaSeries() {
      this.elige = "series";
      this.loadData();
    },
    buscaPelis() {
      this.elige = "pelis";
      this.loadData();
    }
  }
};
</script>

<style lang="scss" scoped>
.row > div {
  padding: 10px 15px;
}
.row + .row {
  margin-top: 1rem;
}
.margen {
  margin: 5%;
}
.claseInput {
  padding: 40px;
  margin-top: 20px;
}
.tarjeta {
  display: inline;
  max-width: 40%;
  margin: 0 auto;
  margin-top: 5%;
}
.titulo {
  font-size: 2.5vw;
}
.anio {
  font-size: 2vw;
}
</style>
