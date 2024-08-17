<template>
    <div class="CardPokemon text-center">
      <div class=" m-3 p-5 border border-warning rounded" style="width: 18rem">
        <img :src="imgPoke" class="card-img-top imgPoke" :class="{visible:!mostrar}"  draggable="false"/>
        <div class="card-body">
          <div v-if="mostrar">
            <!-- <div v-if="mostrar"> -->
            <h5 class="card-title">
              <strong>{{ nombrePoke }}</strong>
            </h5>
          </div>
          <div v-else>
            <div class="card-text py-3">
              <input class="text-center" type="text" v-model="respuestaPoke" />
            </div>
            <button class="btn btn-dark" @click="enviarPoke">
              Descubrir
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>

<script>
export default {
    name: 'CardPokemon',
    props: {
        imgPoke: String,
        nombrePoke: String,
        mostrar: Boolean
    },
    data() {
        return {
            respuestaPoke: '',
        }
    },
    methods: {
      enviarPoke: function () {
            if(this.nombrePoke === this.respuestaPoke){
                this.$emit('descubrir') // Notifica al padre para descubrir el Pokémon
                this.respuestaPoke = ''
            }else{
                alert(`Ups, te haz equivocado, aquí tienes una pista: ${this.nombrePoke.charAt(0)}...`)
            }
        }
    },
    computed:{

    }
}
</script>

<style scoped>
.imgPoke{
    max-height: 100px;
    filter: drop-shadow(0 0 0.75rem rgb(48, 46, 47));
}
.visible{
    filter: blur(5px) grayscale(100%);
}
.card-title{
  padding: 38px 0;
}
</style>