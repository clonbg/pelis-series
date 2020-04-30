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
      <q-card class="tarjeta col-12 col-sm-6" v-for="(item, index) in arrayMostrar" :key="index">
        <img :src="'https://image.tmdb.org/t/p/original'+item.poster" class="poster" />

        <q-card-section class="block">
          <div class="titulo">{{item.titulo}}</div>
          <div class="anio">{{item.anio}}</div>
        </q-card-section>

        <q-separator spaced inset vertical dark />
        <q-btn-group spread class="footer">
          <!-- <q-btn icon="img:https://cdn.quasar.dev/logo/svg/quasar-logo.svg" /> -->
          <q-btn
            push
            type="a"
            :href="'https://www.elitetorrent.nl/?s='+item.titulo.toLowerCase().split(' ').join('+')+'&x=0&y=0'"
            label="Castellano"
            icon="img:statics/icons/spain.svg"
            target="_blank"
            class="letra"
          />
          <q-btn
            push
            type="a"
            :href="'https://wsmmirror.info/Movies/'+item.nombre_original.toLowerCase().split(' ').join('-')+' '"
            label="Inglés"
            icon="img:statics/icons/ingles.svg"
            target="_blank"
            class="letra"
          />
          <q-btn
            push
            type="a"
            :href="'http://www.subswiki.com/search.php?search='+item.nombre_original.toLowerCase().split(' ').join('+')"
            label="Subtítulos"
            icon="subject"
            target="_blank"
            class="letra"
          />
        </q-btn-group>
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
      var strElige;
      if (this.elige == "pelis") {
        strElige = "movie";
      } else if (this.elige == "series") {
        strElige = "tv";
      }
      this.$axios
        .get(
          "https://api.themoviedb.org/3/search/" +
            strElige +
            "?api_key=e1709d76aee13987e8728624138465aa&language=es-ES&query=" +
            String(this.busqueda)
        )
        .then(response => {
          console.log(response.data.results);
          this.arrayMostrar = [];
          response.data.results.forEach(element => {
            if (element.poster_path != null) {
              if (strElige == "movie") {
                var fecha = element.release_date;
                fecha = fecha
                  .split("-")
                  .reverse()
                  .join("-");
                var item = {
                  titulo: element.title,
                  anio: fecha,
                  poster: element.poster_path,
                  nombre_original: element.original_title
                };
              }
              if (strElige == "tv") {
                var fecha = element.first_air_date;
                fecha = fecha
                  .split("-")
                  .reverse()
                  .join("-");
                var item = {
                  titulo: element.name,
                  anio: fecha,
                  poster: element.poster_path,
                  nombre_original: element.original_name
                };
              }
              console.log(item);

              this.arrayMostrar.push(item);
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
  margin: 0 auto;
}
.titulo {
  font-size: 3vw;
}
.anio {
  font-size: 3vw;
}
.footer {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  color: black;
  text-align: center;
  font-size: 2vw;
}
.block {
  padding-bottom: 5vw;
}
.letra {
  font-size: 2vw;
}
.color {
  background-color: green;
  min-width: 400px;
}
</style>
