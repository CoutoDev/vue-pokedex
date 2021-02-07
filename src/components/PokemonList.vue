<template>
  <h1>Pokédex</h1>
  <loader v-if="isLoading" />
  <div
    class="div-pokemon"
    v-for="(pokemon, index) in list"
    :key="index"
    @click="selectPokemon(getPokemonId(pokemon.url))"
  >
    <img :src="getPokemonImage(getPokemonId(pokemon.url))" alt="" />
    {{ pokemon.name }}
  </div>
</template>

<script>
import {
  getPokemonList,
  getPokemonImageUrl,
} from "../service/pokemon-service.js";
import { onMounted, ref } from "vue";
import { useRouter } from "vue-router";
import Loader from "./Loader";

export default {
  components: {
    Loader,
  },
  setup() {
    const list = ref([]);
    const isLoading = ref(false);
    const router = useRouter();

    onMounted(() => {
      isLoading.value = true;
      getPokemonList()
        .then((res) => {
          list.value = res.results;
        })
        .finally(() => {
          isLoading.value = false;
        })
        .catch((err) => {
          console.log(err);
        });
    });

    const getPokemonId = (url) =>
      url.replace("https://pokeapi.co/api/v2/pokemon/", "").replace("/", "");

    const getPokemonImage = (id) => getPokemonImageUrl(id);

    const selectPokemon = (id) =>
      router.push({ name: "pokemon", params: { id } });

    return { 
      list, 
      getPokemonImage, 
      getPokemonId, 
      selectPokemon, 
      isLoading 
    };
  },
};
</script>

<style scoped>
.div-pokemon {
  display: flex;
  align-items: center;
  border: 1px solid lightgray;
  border-radius: 0.2rem;
  margin: 0.5rem;
  cursor: pointer;
}
</style>