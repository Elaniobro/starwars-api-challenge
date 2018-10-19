
<template>
  <v-container fluid grid-list-xl>
    <v-layout wrap align-center>
      <v-flex xs12 sm6 d-flex>
        <v-select
          :items="names"
          label="Character, you choose"
          solo
          v-model="select"
          dark
          append-icon="fas fa-jedi"
          @change="picked"
        ></v-select>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
    import axios from 'axios';
    import data from "../../api/characters.json";
    const names = data.characters.map(character => character.name);
    const urls = data.characters.map(character => character.url)



    export default {
        data: () => ({
            names,
            urls,
            select: null,
            films: [],
            loading: false,
            charData: []
        }),
        methods: {
            picked: function(e) {
                const character = data.characters.find(character => character.name === e );

                this.loading = true;
                this.fetchJson(character.url);
            },
            fetchJson: function (url) {

                axios.get(url)
                .then((response)  =>  {
                    if(response.status !== 200) {
                        console.error(`Error getting Json File. Status Code: ${response.status}`)
                        this.loading = false;
                        return;
                    }
                    this.films = response.data.films;
                    this.fetchFilms(this.films);
                }, (err)  =>  {
                    console.error(err);
                    this.loading = false;
                })
            },
            fetchFilms: function() {
                var self = this;
                axios.all(this.films.map(film => axios.get(film)))
                .then(axios.spread(function (...responses) {
                    // Both requests are now complete
                    console.log(responses);
                    const charData = responses.map(response => response.data);
                    // this.charData = res;
                    console.log(this);
                    console.log(self);
                    self.charData = charData;
                    console.log('\n\n\n\nself.CharData: ', self.CharData);;
                    self.loading = false;
                }, (err) => {
                    console.error(err);
                }));
            }
        }
    }
</script>
