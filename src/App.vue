<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" app>
      <v-list dense>
        <v-list-item link v-for="(page, index) in config.pages" :key="page.title" @click="currentPage = index" :class="{ active: currentPage == index}">
          <v-list-item-content >
            <v-list-item-title v-text="page.title"></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-container class="scroll-y">
      <v-layout align-center justify-center>
        <v-flex xs12>
          <v-app-bar fixed app color="#2481a8"  dark id="scroll-target">
            <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
            <v-btn elevation="5" icon v-if="currentPage" @click="currentPage--"><v-icon>mdi-step-backward</v-icon></v-btn>
            <v-btn elevation="5" icon v-if="currentPage < lastPage" @click="currentPage++"><v-icon>mdi-step-forward</v-icon></v-btn>
            <v-toolbar-title><em>{{ config.author }}:</em> {{ config.title }}</v-toolbar-title>
          </v-app-bar>
          <v-main>
            <Aframe :src="currentUrl"></Aframe>
          </v-main>
        </v-flex>
      </v-layout>
    </v-container>
    <v-footer color="#2481a8" app>
      <span class="white--text">
        Source: {{ base }}
      </span>
    </v-footer>
  </v-app>
</template>

<script>
import Aframe from '@/components/Aframe.vue'

export default {
  name: 'App',

  components: {
    Aframe
  },

  data () {
    return {
      drawer: null,
      config: {
        title: "Notebook Player Site",
        author: "Ingo Dahn",
        pages: [
        {
          "title": "Usage",
          "url": "usage.html"
        },
        {
          "title": "Example",
          "url": "example.html"
        },
        {
          "title": "Example Manifest",
          "url": "manifest.json"
        }
      ]
      },
      base: "https://dahn-research.eu/nbsiteSample",
      currentPage: 0,
      lastPage: 2,
    }
  },
  created () {
    let manifestPath = this.$route.query.path;
    if (manifestPath) {
      this.base = manifestPath;
    }
    fetch(this.base+'/manifest.json')
    .then(async response => {
      const data = await response.json();
      if (!response.ok) {
        const error = (data && data.message) || response.statusText;
        return Promise.reject(error);
      }
      this.config = data;
      this.lastPage = data.pages.length-1;
    })
    .catch(function (error) {
      alert('Error: Could not fetch '+manifestPath+'/manifest.json:'+error.message);
    });
  },
  methods: {
  },
  computed: {
    currentUrl: function () {
      return this.base+"/"+this.config.pages[this.currentPage].url+'?status='+this.config.collectionId;
    },
  },
};


</script>

<style>
.active  {
  background: lightblue;
}

.v-btn {
  margin-right: 1em;
}

</style>
