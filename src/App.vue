<template>
    <div id="app" class="container py-5">
        <div class="text-center">
            <img src="./assets/logoPokemon.png" alt="" class="imgLogo" />
            <p>¿Quién es ese Pokemon?</p>
            <p>Pokemones descubierto : <strong>{{ contador }} / {{ pokemones.length }}</strong></p>
        </div>
        <div class="row py-3">
            <div class="col-3" v-for="(pokemon, index) in pokemones" :key="index">
                <CardPokemon :nombrePoke="pokemon.name" :imgPoke="pokemon.image" @descubrir="mostrarPoke(index)" />
            </div>
        </div>
        <div>
            <nav aria-label="Page navigation example">
                <ul class="pagination">
                    <li class="page-item">
                        <a class="page-link" href="#" aria-label="Previous">
                            <span aria-hidden="true">&laquo;</span>
                        </a>
                    </li>
                    <li class="page-item"><a class="page-link" href="#">1</a></li>
                    <li class="page-item">
                        <a class="page-link" href="#" aria-label="Next" @click="siguiente">
                            <span aria-hidden="true">&raquo;</span>
                        </a>
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
            urlApi: 'https://pokeapi.co/api/v2/pokemon/',
            dataApi: [],
            pokemones: [],
            contador: "0",
            next: '',
            prev: ''
        };
    },
    methods: {
        mostrarPoke: function (index) {
            this.pokemones[index].mostrar = true;
            this.contador++;
        },
        siguiente: function () {
            this.urlApi = this.next

        }
    },
    computed: {
        todosDescubiertos: function () {
            return this.contador === 20;
        },
    },

    async mounted() {
        try {
            const getData = async function (url) {
                let response = await axios.get(url)
                return {
                    image: response.data.sprites.other.dream_world.front_default,
                    name: response.data.name,
                    correcto: false,
                };
            }

            let response = await axios.get(this.urlApi);
            let { data } = response;
            this.next = data.next
            this.prev = data.previous
            Promise.all(data.results.map(pokemon => getData(pokemon.url)))
                .then(pokemones => this.pokemones = pokemones)
                .catch(error => {
                    alert('error en procesar pokemones')
                    console.log(error)
                })
            console.log(this.urlApi)

        } catch (error) {
            if (error.code == "ERR_NETWORK") {
                return alert(
                    "En estos momento el servidor está caído, puede intentar más tarde."
                );
            }
            if (error.response.status == 404) {
                alert("El recurso que está buscando no existe.");
            } else {
                alert("El servidor en estos momentos no puede procesar su solicitud.");
            }
        }
    }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:ital,wght@0,100..700;1,100..700&family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');

#app {
    font-family: 'Roboto', sans-serif;
}

.imgLogo {
    width: 30vw;
    max-height: 100%;
}

span {
    font-family: "Roboto Mono", monospace;
    color: #FFC94A;
}
</style>
