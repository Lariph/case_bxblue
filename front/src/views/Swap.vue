<template>
   <v-container fluid>
             <span>Jogador 1</span>
    <v-row align="center">
      <v-col
        class="d-flex"
        cols="8"
        sm="6"
      >
        <v-select
          :items="pokemonNames"
          label="Escolha seu pokémon"
          v-model="firstPokemon"
        ></v-select>
      </v-col>
      <v-col
        cols='4'
      >
        <v-btn
        class="mx-2"
        fab
        dark
        x-small
        color="primary"
        @click="decreaseFirst()"
        >
            <v-icon dark>
                mdi-minus
            </v-icon>
        </v-btn>

        <span> Quantidade: {{ firstPokemonQuant }} </span>

        <v-btn
        class="mx-2"
        fab
        dark
        x-small
        color="primary"
        @click="increaseFirst()"
        >
            <v-icon dark>
                mdi-plus
            </v-icon>
        </v-btn>
      </v-col>
    </v-row>
<v-divider></v-divider>

      <span>Jogador 2</span>
    <v-row align="center">
      <v-col
        class="d-flex"
        cols="8"
        sm="6"
      >
        <v-select
          :items="pokemonNames"
          label="Escolha seu pokémon"
          v-model="secondPokemon"
        ></v-select>
      </v-col>
      <v-col
        cols='4'
      >
        <v-btn
        class="mx-2"
        fab
        dark
        x-small
        color="primary"
        @click="decreaseSecond()"
        >
            <v-icon dark>
                mdi-minus
            </v-icon>
        </v-btn>

        <span> Quantidade: {{ secondPokemonQuant }} </span>

        <v-btn
        class="mx-2"
        fab
        dark
        x-small
        color="primary"
        @click="increaseSecond()"
        >
            <v-icon dark>
                mdi-plus
            </v-icon>
        </v-btn>
      </v-col>
    </v-row>

    <v-row align="center">
    <v-btn
      class="ma-2"
      color="primary"
      @click="submit()"
    >
      Essa troca é justa?
    </v-btn>
    </v-row>
    
    <v-row align="center">
    <v-alert
      border="right"
      colored-border
      type="error"
      elevation="2"
      class="ma-2"
      v-show="showJogador2"
    >
      Essa troca é injusta para o jogador 2
    </v-alert>

    <v-alert
      border="right"
      colored-border
      type="error"
      elevation="2"
      class="ma-2"
      v-show="showJogador1"
    >
      Essa troca é injusta para o jogador 1
    </v-alert>

    <v-alert
      border="top"
      colored-border
      type="info"
      elevation="2"
      class="ma-2"
      v-show="showJogadores"
    >
      Essa troca é justa
    </v-alert>

    </v-row>

    <history
        :exchanges="exchanges"
    ></history>

   </v-container>
</template>

<script>
import History from '../components/History.vue'

export default {
    name: 'Swap',
    components: {
       History,
    },
    data () {
      return {
        pokemonList: [],
        pokemonNames: [],
        firstPokemon: null,
        firstPokemonQuant: 0,
        firstBaseExperience: null,
        secondPokemon: null,
        secondPokemonQuant: 0,
        secondBaseExperience: null,
        showJogador2: false,
        showJogador1: false,
        showJogadores: false,
        exchanges: [],
     }
   },
   beforeMount() {
       this.getPokemons()
   },
   methods: {
       increaseSecond() {
        if (this.secondPokemonQuant < 6) {
           this.secondPokemonQuant = this.secondPokemonQuant + 1
        }
       },

       decreaseSecond() {
        this.secondPokemonQuant = this.secondPokemonQuant - 1
        if (this.secondPokemonQuant < 0) {
               this.secondPokemonQuant = 0
        }
       },
       increaseFirst() {
        if (this.firstPokemonQuant < 6) {
            this.firstPokemonQuant = this.firstPokemonQuant + 1
        }
       },
       decreaseFirst() {
        this.firstPokemonQuant = this.firstPokemonQuant - 1
        if (this.firstPokemonQuant < 0) {
            this.firstPokemonQuant = 0
        }
       }, 
       getPokemons() {
        this.axios.get('https://pokeapi.co/api/v2/pokemon/').then((response) => {
            this.pokemonList = response.data.results
            this.pokemonList.forEach(elem => this.pokemonNames.push(elem.name))
        })
       },
       submit() {
           const firstUrl = this.pokemonList.find(elem => elem.name === this.firstPokemon).url
           const secondUrl = this.pokemonList.find(elem => elem.name === this.secondPokemon).url
           this.axios.get(firstUrl).then((response) => {
               this.firstBaseExperience = response.data.base_experience
               this.axios.get(secondUrl).then((response) => {
                    this.secondBaseExperience = response.data.base_experience
                               const firstValue = this.firstBaseExperience * this.firstPokemonQuant
                               const secondValue = this.secondBaseExperience * this.secondPokemonQuant
                               const difference = firstValue - secondValue
                               if (difference < 0 ) {
                                   this.showJogador1 = false
                                   this.showJogadores = false
                                   this.showJogador2 = true
                                    this.exchanges.push(
                                    {
                                    jogador1: 
                                        { pokemon: this.firstPokemon,
                                          quantidade: this.firstPokemonQuant, 
                                          base_experience: this.firstBaseExperience,
                                        },
                                    jogador2:
                                        { pokemon: this.secondPokemon,
                                          quantidade: this.secondPokemonQuant, 
                                          base_experience: this.secondBaseExperience,
                                        },
                                    exchange: 'Injusta para o jogador 2',
                                    })
                                }
                                if (difference >= 0 && difference <= 50) {
                                   this.showJogador1 = false
                                   this.showJogadores = true
                                   this.showJogador2 = false
                                   this.exchanges.push(
                                    {
                                    jogador1: 
                                        { pokemon: this.firstPokemon,
                                          quantidade: this.firstPokemonQuant, 
                                          base_experience: this.firstBaseExperience,
                                        },
                                    jogador2:
                                        { pokemon: this.secondPokemon,
                                          quantidade: this.secondPokemonQuant, 
                                          base_experience: this.secondBaseExperience,
                                        },
                                    exchange: 'Justa para os dois',
                                    })
                                }
                                if (difference > 50) {
                                   this.showJogador1 = true
                                   this.showJogadores = false
                                   this.showJogador2 = false
                                   this.exchanges.push(
                                    {
                                    jogador1: 
                                        { pokemon: this.firstPokemon,
                                          quantidade: this.firstPokemonQuant, 
                                          base_experience: this.firstBaseExperience,
                                        },
                                    jogador2:
                                        { pokemon: this.secondPokemon,
                                          quantidade: this.secondPokemonQuant, 
                                          base_experience: this.secondBaseExperience,
                                        },
                                    exchange: 'Injusta para o jogador 1',
                                    })
                                }
               })
           })
       },
   },

}
</script>
