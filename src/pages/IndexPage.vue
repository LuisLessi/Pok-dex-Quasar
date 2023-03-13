<template>
  <q-page class="flex flex-center">
    <div class="column items-center">
      <h2>{{ name }}</h2>
      <q-img :src="url" width="250px" />
      <h5>HP: {{ hp }}</h5>
    </div>
    <div class="row justify-around full-width">
      <q-input filled v-model="search" label="Digite nome de um pokemon" />
      <q-btn color="purple" label="Pesquisar" @click="getPokemon" />
    </div>
    <div class="row justify-between absolute full-width container-arrows">
      <q-icon name="fa-solid fa-angle-left" color="primary" 
      size="50px" class="q-ml-sm cursor-pointer" @click="getPokemon(id - 1)">
        <q-tooltip>
          Anterior
        </q-tooltip>
      </q-icon>

      <q-icon name="fa-solid fa-angle-right" color="primary" 
      size="50px" class="q-mr-sm cursor-pointer" @click="getPokemon(id + 1)">
        <q-tooltip>
          Próximo
        </q-tooltip>
      </q-icon>
    </div>
  </q-page>
</template>

<script>
import { api } from "../boot/axios"

export default {
  name: 'IndexPage',

  data() {
    return {
      name: "",
      url: "",
      search: 'ditto',
      hp: "",
      id: null,
    }
  },
  async beforeMount() {
    await this.getPokemon()
  },

  methods: {
    async getPokemon(id) {
      try {
        const response = await api.get(id > 0 ? `/pokemon/${id}/` : `/pokemon/${this.search}/`)
        console.log(response)
        this.name = response.data.name;
        this.search = response.data.name;
        this.url = response.data.sprites.other.dream_world.front_default;
        this.id = response.data.id
        // Encontre o objeto de estatísticas com nome "hp"
        const hpStat = response.data.stats.find(stat => stat.stat.name === "hp");
        if (hpStat) {
          // Atribua o valor de HP ao campo "hp"
          this.hp = hpStat.base_stat;
        } else {
          this.hp = "N/A";
        }
      } catch (error) {
        this.triggerNegative();
      }
    },

    triggerNegative() {
      this.$q.notify({
        type: 'negative',
        position: "top",
        message: 'Pokemon não identificado.'
      })
    },
  },

}
</script>

<style lang="scss" scoped>
.container-arrows {}
</style>