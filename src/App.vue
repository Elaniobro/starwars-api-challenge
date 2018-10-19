<template>
  <div id="app">
    <ToolBar/>
        <v-container>
          <v-layout>
            <v-flex>
              <v-select
                :items="names"
                label="Character, you choose"
                solo
                v-model="select"
                dark
                append-icon="fas fa-jedi"
                @change="picked"
                :error="false"
              ></v-select>
            </v-flex>
          </v-layout>
        </v-container>
        <span id="isLoading" v-if="isLoading">
          <img src="./assets/loading-1.gif" height="100"/>
        </span>
        <section v-if="isError">
          <div class="card--film__container">
            <p class="card--film__error subheading">Error trying to request data file of 'unkown'. Please select another character</p>
          </div>
        </section>
        <section v-if="isLoading == false && charData.length > 0"  class="card--film__section">
          <div class="card--film__container"  v-for="(charData, index) in charData" :key="index">
            <div class="blackout">
               <div class="logo--img__container">
                  <img class="logo--img__logo" :src="getImgUrl(0)" v-bind:alt="0">
                </div>
            </div>
            <div class="logo--img__container">
              <img class="logo--img__logo"
                :src="getImgUrl(charData.episode_id)"
                v-bind:alt="charData.episode_id">
            </div>
            <div class="crawl-text--film__container">
              <p class="card--film__date subheading">Released: {{ [ charData.release_date, "YYYY-MM-DD" ] | moment("dddd, MMMM Do YYYY") }}</p>
              <p class="card--film__director subheading">Directed by: {{ charData.director }}</p>
              <p class="card--film__producer subheading">Producer(s): {{ charData.producer }}</p>
            </div>
            <div class="titles">
              <div class="titlecontent">
                <p>{{ charData.opening_crawl }}</p>
              </div>
            </div>
          </div>
        </section>
    <Footer/>
  </div>
</template>

<style>
.v-menu {
  position: absolute;
}
.v-menu>.v-menu__content[style] {
  position: absolute;
  margin-left: -12px;
  margin-top: -24px;
  top:0 !important;
  left:0 !important;
}
body {
  background-position-x: right;
  background-size: contain;
  background-attachment: fixed;
  background: url('./assets/bg-4.png') #000 no-repeat;
}
#app {
  height: 100vh;
  min-height: 100%;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.card--film__section{
  position: relative;
  display: grid;
  grid-template-columns: repeat(auto-fit, minMax(300px, 1fr));
  grid-gap: 2rem;
  margin-bottom: 10rem;
  padding: 1rem;
}
.card--film__date,
.card--film__director,
.card--film__producer,
.card--film__error {
  color: #fff;
}
.blackout {
  position: absolute;
  width: 100%;
  height: 100%;
  opacity: 0;
  background: #000;
  margin:-1rem;
}
.card--film__container {
  background: #000;
  position: relative;
  border-radius: 5px;
  border: 1px solid  #fff;
  padding: 1rem;
  box-shadow: 0px 0px 10px #c3c3c3;
  overflow: hidden;
}
.card--film__title {
  color: #fff;
}
.logo--img__container {
  min-height: 210px;
  margin: auto;
  display: grid;
  justify-content: center;
  align-content: center;
}
.titles {
  opacity: 0;
  position: absolute;
  width: 28rem;
  height: 60rem;
  bottom: 0;
  left: 50%;
  margin-left: -14rem;
  font-size: 200%;
  font-weight: bold;
  text-align: justify;
  overflow: hidden;
  transform-origin: 50% 100%;
  transform: perspective(300px) rotateX(25deg);
  color: #ffc400;
}
.titles:after {
  position: absolute;
  content: ' ';
  left: 0;
  right: 0;
  top: 0;
  bottom: 60%;
  background-image: linear-gradient(top, rgba(0,0,0,1) 0%, transparent 100%);
  pointer-events: none;
}
.card--film__container:hover .titlecontent {
  position: absolute;
  top: 10%;
  animation: scroll 60s linear 0s infinite;
}
.card--film__container:hover .blackout,
.card--film__container:hover .titles {
  opacity: 1;
}
.blackout img {
  position: relative;
  margin-top: 8rem;
  transform: scale(3);
  transition: all 2s ease-in-out;
  z-index: 200
}
.card--film__container:hover .blackout img {
  transform: scale(0);
}
@keyframes scroll {
  0% { top: 100%; }
  100% { top: -170%; }
}
</style>

<script>
import Vue from 'vue';
import axios from 'axios';
import ToolBar from './components/ToolBar';
import Footer from './components/Footer';
import data from '../api/characters.json';

const names = data.characters.map(character => character.name);
const urls = data.characters.map(character => character.url);


export default {
  name: 'App',
  data: () => ({
    names,
    urls,
    select: 'Sky Walker',
    films: [],
    isError: false,
    isLoading: false,
    charData: [],
  }),
  components: {
    ToolBar,
    Footer,
  },
  methods: {
    picked: function (e) {
      const character = data.characters.find(character => character.name === e);

      this.isLoading = true;
      this.fetchJson(character.url);
    },
    fetchJson: function (url) {
      axios.get(url)
        .then((response) => {
          if (response.status !== 200) {
            Vue.nextTick(function () {
              this.isLoading = false;
            });
            this.isError = true;
            return;
          }
          this.isError = false;
          this.films = response.data.films;
          this.fetchFilms(this.films);
        }, (err) => {
          console.error(err);
          this.isError = true;
          this.isLoading = false;
          this.charData = [];
        });
    },
    fetchFilms: function () {
      let self = this;
      axios.all(this.films.map(film => axios.get(film)))
        .then(axios.spread(function (...responses) {
          const charData = responses.map(response => response.data);
          Vue.nextTick(function () {
            self.charData = charData;
            self.isLoading = false;
          });
        }, (err) => {
          console.error(err);
        }));
    },
    getImgUrl: function (episodeId) {
      return require(`./assets/logo/episode-${episodeId}.png`);
    },
  },
};
</script>
