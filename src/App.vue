<template>
  <v-app>
    <v-container>
      <v-container> 
        <v-text-field 
        v-model="search"
        label="Pesquisar" 
        placeholder="Pikachu"
        ></v-text-field>  

        <v-row>
          <v-col cols="3" v-for="pokemon in filtered_pokemons" :key="pokemon.name">
            <v-card @click="show_pokemon(get_id(pokemon))">
              <v-container>
                <v-row class="mx-0 d-flex justify-center">
                  <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(pokemon)}.png`"
                  :alt="pokemon.name"
                  width="80%"
                  />
                </v-row>
                <h2 class="text-center">{{ get_name(pokemon) }}</h2>
              </v-container>
            </v-card>            
          </v-col>
        </v-row>
      </v-container>      
    </v-container>

    <v-dialog
      v-model="show_dialog"
      width="800"
    >     
      <v-card>
        <v-container>
          {{ selected_pokemon }}
        </v-container>

      </v-card>
    </v-dialog>

  </v-app>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',

  components: {
   
  },

  data() {
    return {
      pokemons: [],
      search: "",
      show_dialog: false, //para exibição do card assim que clicamos no pokemon
      selected_pokemon: null, //conteudo do card assim que clicamos no pokemon
    }
  },

  mounted() {
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=151").then((response) => {
      this.pokemons = response.data.results;
    });
  },
  methods: {
    get_id(pokemon) {
      return Number(pokemon.url.split("/")[6]);
    },
    get_name(pokemon) {
      //captura o nome e deixa a primeira letra maiuscula
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    show_pokemon(id) {
      //exibe a info do pokemon selecionado dentro do card

      axios
      .get(`https://pokeapi.co/api/v2/pokemon/${id}`)
      .then((response) => {
        this.selected_pokemon = response.data;
        this.show_dialog = !this.show_dialog;
      });
      
    }
  },
  computed: {
    filtered_pokemons() {
      //filtra o pokemon selecionado baseado no que é digitado no input, foi incluido no v-for
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search);
      })
    }
  }

};
</script>
<style scoped>
#app {
  /* background da pagina html */
  background: linear-gradient(
      to bottom right,
      rgba(10, 10, 10, 1),
      rgba(12, 39, 63, 1)
    )
    no-repeat center center fixed !important;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
}
</style>