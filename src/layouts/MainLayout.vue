<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />

        <q-toolbar-title>
          Pelis y series
        </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      content-class="bg-grey-1"
    >
      <q-list>
        <q-item-label
          header
          class="text-grey-8"
        >
          Enlaces
        </q-item-label>
        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink'

export default {
  name: 'MainLayout',

  components: {
    EssentialLink
  },

  data () {
    return {
      leftDrawerOpen: false,
      essentialLinks: [
        {
          title: 'Mejortorrent',
          caption: 'Series y películas en castellano',
          icon: 'img:http://www.mejortorrentt.net/imagenes_web/bicho1.jpg',
          link: 'http://www.mejortorrentt.net/'
        },
        {
          title: 'Elitetorrent',
          caption: 'Series y películas en castellano',
          icon: 'img:statics/icons/elitetorrent.png',
          link: 'https://www.elitetorrent.nl/'
        },
        {
          title: 'Wsmmirror',
          caption: 'Series y películas en inglés',
          icon: 'img:statics/icons/wsmmirror.png',
          link: 'https://wsmmirror.info/'
        },
        {
          title: 'Eztv',
          caption: 'Series en inglés',
          icon: 'img:statics/icons/eztv.png',
          link: 'https://eztv.io/'
        },
        {
          title: 'Subswiki',
          caption: 'Subtítulos de series y películas',
          icon: 'img:statics/icons/subswiki.png',
          link: 'http://www.subswiki.com/'
        },
        {
          title: 'Opensubtitles',
          caption: 'Subtítulos de series y películas',
          icon: 'img:statics/icons/opensubtitles.png',
          link: 'https://www.opensubtitles.org/es'
        }
      ]
    }
  }
}
</script>
