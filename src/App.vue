<template>
  <div id="app" class="container py-5">
    <div class="text-center">
      <img src="./assets/logoPokemon.png" alt="" class="imgLogo" />
      <p>¿Quién es ese Pokemon?</p>
      <p>
        Pokemones descubierto :
        <strong>{{ contador }} / {{ pokemones.length }}</strong>
      </p>
    </div>
    <div class="row py-3">
      <div class="col-3" v-for="(pokemon, index) in pokemones" :key="index">
        <CardPokemon
        :imgPoke="pokemon.image"
      :nombrePoke="pokemon.name"
      :mostrar="pokemon.mostrar"  
      @descubrir="mostrarPoke(index)"
        />
      </div>
    </div>
    <div class="d-flex justify-content-center">
      <nav aria-label="Page navigation example">
        <ul class="pagination">
          <li class="page-item">
            <a class="page-link" href="#" @click="anterior">Previous</a>
          </li>
          <li class="page-item">
            <a class="page-link" href="#" @click="siguiente">Next</a>
          </li>
        </ul>
      </nav>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import CardPokemon from "./components/CardPokemon";

export default {
  name: "App",
  components: {
    CardPokemon,
  },
  data() {
    return {
      urlApi: "https://pokeapi.co/api/v2/pokemon/",
      pokemones: [],
      contador: 0,
      next: "",
      prev: "",
      discoveredPokemons: {}, // Almacena el estado de descubrimiento de los Pokémon
    };
  },
  methods: {
    mostrarPoke(index) {
      const pokemon = this.pokemones[index];
      // Actualizar el estado del Pokémon a "mostrado"
      this.$set(this.pokemones, index, { ...pokemon, mostrar: true });
      // Guardar el estado en discoveredPokemons
      this.$set(this.discoveredPokemons, pokemon.name, true);
      this.contador++;
    },
    async fetchPokemonData() {
      try {
        const getData = async (url) => {
          const response = await axios.get(url);
          return {
            image: response.data.sprites.other.dream_world.front_default,
            name: response.data.name,
            correcto: false,
            mostrar: false, // Inicialmente, no mostrado
          };
        };

        const response = await axios.get(this.urlApi);
        const data = response.data;

        this.next = data.next;
        this.prev = data.previous;

        const pokemones = await Promise.all(
          data.results.map((pokemon) => getData(pokemon.url))
        );

        // Actualizar el estado 'mostrar' de los Pokémon si ya fueron descubiertos
        pokemones.forEach((pokemon) => {
          if (this.discoveredPokemons[pokemon.name]) {
            pokemon.mostrar = true;
          }
        });

        this.pokemones = pokemones;
      } catch (error) {
        if (error.code === "ERR_NETWORK") {
          alert(
            "En estos momentos el servidor está caído, puede intentar más tarde."
          );
        } else if (error.response && error.response.status === 404) {
          alert("El recurso que está buscando no existe.");
        } else {
          alert(
            "El servidor en estos momentos no puede procesar su solicitud."
          );
        }
        console.error(error);
      }
    },
    siguiente() {
      if (this.next) {
        this.urlApi = this.next;
        this.fetchPokemonData(); // Llamar a fetchPokemonData para cargar la siguiente página
      } else {
        alert("No hay más páginas disponibles.");
      }
    },
    anterior() {
      if (this.prev) {
        this.urlApi = this.prev;
        this.fetchPokemonData(); // Llamar a fetchPokemonData para cargar la página anterior
      } else {
        alert("No hay páginas anteriores disponibles.");
      }
    },
  },
  computed: {
    todosDescubiertos() {
      return this.contador === this.pokemones.length;
    },
  },
  created() {
    this.fetchPokemonData(); // Llamar a la función al crear el componente
  },
};
</script>



<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto+Mono:ital,wght@0,100..700;1,100..700&family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap");

#app {
  font-family: "Roboto Mono", monospace;
}
body {
  background-image: url(./assets/peakpx.jpg);
  background-repeat: no-repeat;
  background-size: cover;
}

.imgLogo {
  width: 30vw;
  max-height: 100%;
}

span {
  font-family: "Roboto Mono", monospace;
  color: #ffc94a;
}
</style>
