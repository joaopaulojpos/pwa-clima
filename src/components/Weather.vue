<template>
  <v-container v-if="msgErro">
    <v-alert
      type="error"
      title="Erro"
    >
      {{ msgErro }}
    </v-alert>
  </v-container>

  <v-container v-if="!msgErro" class="grey-lighten-4">
    <div class="text-center">
    <p class="text-h6 font-weight-bold text-center">{{dataSet.cidade}}, {{ dataSet.pais }}</p>
    <img :src="dataSet.icone" alt="icone clima" height="210" width="210">
    <h2 class="text-h1 text-center">{{ dataSet.temperatura }}°C</h2>
    <h6 class="text-h6 font-weight-regular text-center">{{ dataSet.descricao }}</h6>
    </div>
  </v-container>

  <v-container v-if="!msgErro">
      <v-row class="text-center">

        <v-col cols="4">
          <v-card class="pt-2 text-white" color="#424242">
            <v-card-subtitle> 
              <p class="text-white text-subtitle-2"> Máxima </p>
          </v-card-subtitle>
          <v-card-text> 
            <p class="text-h6"> {{ dataSet.temperaturaMaxima }}°C </p>
          </v-card-text>
          </v-card>
        </v-col>

        <v-col cols="4">
          <v-card class="pt-2 text-white" color="#424242">
            <v-card-subtitle> 
              <p class="text-subtitle-2"> Mínima </p>
          </v-card-subtitle>
          <v-card-text> 
            <p class="text-h6"> {{ dataSet.temperaturaMinima }}°C </p>
          </v-card-text>
          </v-card>
        </v-col>

        <v-col cols="4">
          <v-card class="pt-2 text-white" color="#424242">
            <v-card-subtitle> 
              <p class="text-subtitle-2"> Umidade </p>
          </v-card-subtitle>
          <v-card-text> 
            <p class="text-h6"> {{ dataSet.umidade }}% </p>
          </v-card-text>
          </v-card>
        </v-col>

      </v-row>
    </v-container>

</template>

<script setup>
import { ref, reactive } from 'vue';
import api from '@/services/api'

const latitude = ref(null)
const longitude = ref(null)
const msgErro = ref(null)
const dataSet = reactive({
  cidade: null,
  pais: null,
  temperatura: null,
  temperaturaMinima: null,
  temperaturaMaxima: null,
  descricao: null,
  icone: null,
  umidade: null
})

localizacao()

async function localizacao(){
  if(navigator.geolocation){
    navigator.geolocation.getCurrentPosition(position => {
      latitude.value = position.coords.latitude
      longitude.value = position.coords.longitude
      climaAtual()
    })
  } else {
    msgErro.value = "Por favor, verifique se sua localização está ligada e autorizada"
  }
}

async function climaAtual(){
  try{
    const response = await api.get('/weather', {
      params: {
        lat:latitude.value,
        lon:longitude.value,
        units:'metric',
        lang:'pt_br',
        appid:'a74a91c8b59be54cd0a1e8cb8229219c'
      }
    })
    dataSet.cidade = response.data.name
    dataSet.pais = response.data.sys.country
    dataSet.temperatura = Math.round(response.data.main.feels_like)
    dataSet.temperaturaMaxima = Math.round(response.data.main.temp_max)
    dataSet.temperaturaMinima = Math.round(response.data.main.temp_min)
    dataSet.descricao = response.data.weather[0].description
    dataSet.icone = 'https://raw.githubusercontent.com/joaopaulojpos/pwa-clima/main/src/assets/'+response.data.weather[0].icon+'.png'
    dataSet.umidade = response.data.main.humidity
  } catch (error) {
    msgErro.value = error.message
  }
}

</script>