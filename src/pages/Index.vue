<template>

  <q-page class="margen">
    <q-input v-model="busqueda"
             type="text"
             label="Busqueda"
             value="pelis"
             @change="loadData"
             @keyup.enter="loadData"
             @click="busqueda=null"
             class="claseInput" />
    <div class="row">
      <div class="col-12">
        <q-btn color="blue" class="full-width" label="Buscar" @click="loadData" />
      </div>
    </div>

    <div class="row">
      <div class="col-6">
        <q-btn color="green"
               class="full-width"
               label="Peliculas"
               :disable="elige=='pelis'"
               @click="buscaPelis" />
      </div>
      <div class="col-6">
        <q-btn color="green"
               class="full-width"
               label="Series"
               :disable="elige=='series'"
               @click="buscaSeries" />
      </div>
    </div>

    <div class="row q-gutter-xs">
      <q-card v-if="arrayMostrar.length>0" class="tarjeta col-10 col-sm-5" v-for="(item, index) in arrayMostrar" :key="index">
        <img :src="'https://image.tmdb.org/t/p/original'+item.poster" class="poster" />

        <q-card-section>

          <div>
            <div class="titulo">{{item.titulo}}</div>
            <strong class="anio">{{item.anio}}</strong>
            <strong class="valor">
                                  <span class="material-icons">
                                    thumb_up_alt
                                  </span> {{item.popularidad}}
                                </strong>
          </div>
        </q-card-section>
        <div class="space"></div>
        <div class="card-footer q-pa-md">
          <q-btn-group spread>
            <q-btn push
                   type="a"
                   :href="'https://www.elitetorrent.nl/?s='+item.titulo.toLowerCase().split(' ').join('+')+'&x=0&y=0'"
                   color="white"
                   text-color="black"
                   label="SPA"
                   icon="img:statics/icons/spain.png"
                   target="_blank"
                   class="icon-footer"
                   stack />
            <q-btn push
                   type="a"
                   :href="'https://wsmmirror.info/Movies/'+item.nombre_original.toLowerCase().split(' ').join('-')+' '"
                   color="white"
                   text-color="black"
                   label="ING"
                   icon="img:statics/icons/ingles.png"
                   target="_blank"
                   class="icon-footer"
                   stack />

            <q-btn push
                   type="a"
                   :href="'http://www.subswiki.com/search.php?search='+item.nombre_original.toLowerCase().split(' ').join('+')"
                   color="white"
                   text-color="black"
                   label="SUB"
                   icon="subject"
                   target="_blank"
                   class="icon-footer"
                   stack />
            <q-btn push
                   type="a"
                   :href="'https://www.filmaffinity.com/es/search.php?stext='+item.titulo.toLowerCase().split(' ').join('+')"
                   color="white"
                   text-color="black"
                   label="FILM"
                   icon="img:statics/icons/film.png"
                   target="_blank"
                   class="icon-footer"
                   stack />
          </q-btn-group>
        </div>
      </q-card>
      <div v-show="arrayMostrar.length==0" class="divSin">
        <q-icon name="img:statics/icons/vacio.png" class="logo" size="xl" />
        <h6>Sin resultados</h6>
      </div>

    </div>
  </q-page>

</template>

<script>

  import Vue from 'vue'
  import axios from 'axios'
  import AxiosPlugin from 'vue-axios-cors'

  Vue.use(AxiosPlugin)
  export default {
    name: 'PageIndex',
    data() {
      return {
        busqueda: null,
        arrayMostrar: [],
        elige: 'pelis'
      }
    },
    methods: {
      loadData() {
        var strElige
        if (this.elige == 'pelis') {
          strElige = 'movie'
        } else if (this.elige == 'series') {
          strElige = 'tv'
        }
        console.log('console busqueda: ' + this.busqueda.length)
        if (this.busqueda.trim().length == 0) {
          console.log('cerooooo')
          this.arrayMostrar = []
        } else {
          this.$axios
            .get(
              'https://api.themoviedb.org/3/search/' +
                strElige +
                '?api_key=e1709d76aee13987e8728624138465aa&language=es-ES&query=' +
                String(this.busqueda.trim())
            )
            .then(response => {
              console.log(response.data.results)
              this.arrayMostrar = []
              response.data.results.forEach(element => {
                if (element.poster_path != null) {
                  if (strElige == 'movie') {
                    var fecha = element.release_date
                      .split('-')
                      .reverse()
                      .join('-')
                    var item = {
                      titulo: element.title,
                      anio: fecha,
                      poster: element.poster_path,
                      nombre_original: element.original_title,
                      popularidad: parseFloat(element.popularity)
                    }
                  }
                  if (strElige == 'tv') {
                    var fecha = element.first_air_date
                      .split('-')
                      .reverse()
                      .join('-')
                    var item = {
                      titulo: element.name,
                      anio: fecha,
                      poster: element.poster_path,
                      nombre_original: element.original_name,
                      popularidad: parseFloat(element.popularity)
                    }
                  }
                  console.log(item)
                  this.arrayMostrar.push(item)
                }
                this.arrayMostrar.sort(function(a, b) {
                  //ordenar
                  if (a.popularidad < b.popularidad) {
                    return 1
                  }
                  if (a.popularidad > b.popularidad) {
                    return -1
                  }
                  // a must be equal to b
                  return 0
                })
              })
            })
            .catch(e => {
              console.log(e)
            })
        }
      },
      buscaSeries() {
        this.elige = 'series'
        this.loadData()
      },
      buscaPelis() {
        this.elige = 'pelis'
        this.loadData()
      }
    }
  }

</script>

<style lang="scss" scoped>

  body {
    margin: 0;
  }

  .row > div {
    padding: 10px 10px;
  }
  .row + .row {
    margin-top: 1rem;
  }
  .margen {
    margin: 0;
  }
  .claseInput {
    padding: 40px;
    margin-top: 20px;
  }
  .tarjeta {
    display: inline;
    margin: 0 auto;
    margin-top: 2.5%;
    margin-bottom: 2.5%;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
  }
  .titulo {
    font-size: 2.6vw;
  }
  .anio {
    font-size: 2vw;
  }
  .color {
    background-color: green;
    min-width: 400px;
  }
  .divSin {
    margin: 0 auto;
  }
  .logo {
    display: flex;
    margin: 0 auto;
    margin-top: 10vh;
  }
  .valor {
    display: inline;
    float: right;
    font-size: 2vw;
  }
  .space {
    flex: 1;
  }
  .icon-footer{
    margin-bottom: -3vH
  }
  .q-byn-group{
    box-shadow: 0 1px 5px rgba(0, 0, 0, 0), 0 2px 2px rgba(0, 0, 0, 0), 0 3px 1px -2px rgba(0, 0, 0, 0);
  }
</style>
