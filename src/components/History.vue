<template>
  <v-container fluid>
   <v-data-table
      :headers="headers"
      :items="exchanges"
      item-key="name"
      class="elevation-1"
    >
    </v-data-table>

  </v-container>
</template>

<script>
export default {
    props: {
        exchanges: {
            type: Array, 
            required: true,
        }
    }, 
    computed: {
      headers () {
        return [
          { text: 'Jogador 1', value: 'jogador1.pokemon' },
          { text: 'Jogador 2', value: 'jogador2.pokemon' },
          { text: 'Quantidade Jogador 1', value: 'jogador1.quantidade' },
          { text: 'Quantidade Jogador 2', value: 'jogador2.quantidade' },
          { text: 'Base Experience Jogador 1', value: 'jogador1.base_experience' },
          { text: 'Base Experience Jogador 2', value: 'jogador2.base_experience' },
          { text: 'Justa?', value: 'exchange' },
        ]
      },
    },

}
</script>

<style>

</style>