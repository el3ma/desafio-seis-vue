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
        
    },
    data() {
        return {
            respuestaPoke: '',
            mostrar: false
        }
    },
    methods: {
        enviarPoke: function () {
            if(this.nombrePoke === this.respuestaPoke){
                this.$emit('descubrir')
                this.mostrar = !this.mostrar
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
}
.visible{
    filter: blur(5px) grayscale(100%);
}
</style>