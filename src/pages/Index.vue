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
        <img :src="item.Poster" class="poster" />

        <q-card-section>
          <div class="titulo">{{item.Title}}</div>
          <div class="anio">{{item.Year}}</div>
        </q-card-section>
      </q-card>
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
      this.$axios
        .get(
          "http://www.omdbapi.com/?apikey=6065e9e4&s=" + String(this.busqueda)
        )
        .then(response => {
          console.log(response.data.Search);
          var arraySeries = [];
          var arrayPelis = [];
          response.data.Search.forEach(element => {
            if (element.Type == "series") {
              arraySeries.push(element);
            }
            if (element.Type == "movie") {
              arrayPelis.push(element);
            }
            if (this.elige == "series") {
              this.arrayMostrar = arraySeries;
            } else if (this.elige == "pelis") {
              this.arrayMostrar = arrayPelis;
            }
          });
        })
        .catch(e => {
          console.log(e);
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
