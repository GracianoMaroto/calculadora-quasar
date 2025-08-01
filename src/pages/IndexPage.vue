<template>
  <q-page padding>
    
    <div class="row justify-center">
      <div class="col-12 col-md-8">
        <q-card>

          <q-card-section class="bg-primary text-white">
            <div class="text-h6">Calculadora</div>
          </q-card-section>
          
          <q-card-section >
            <div class="text-h5 text-grey-5 text-right">
            {{ acumulador + atual }}
            </div>

            <div class="text-h3 text-right">
              {{ resultado }}
            </div>
            </q-card-section>

            <q-card-section class="bg-grey-4">
              <div class="row q-col-gutter-sm">
                
                <div class="col-3" v-for="(btn , index) in botoes" :key="index">
                  
                  <q-btn 
                  class="full-width text-h6" 
                  :color="naoNumero(btn) ? 'indigo' : 'grey-2'" 
                  :text-color="naoNumero(btn) ? 'white' : 'grey-8'"
                  @click="btnAction(btn)"
                  >
                    {{btn}}
                  </q-btn>
                </div>

                <div class="col-6">
                  <q-btn class="full-width text-h6" color="indigo" @click="btnReset">
                    Clear
                  </q-btn>
                </div>

                <div class="col-6">
                  <q-btn class="full-width text-h6" color="orange" @click="btnResultado">
                    =
                  </q-btn>
                </div>
              </div>
            </q-card-section>
        </q-card>
      </div>
    </div>


  </q-page>
</template>

<script>
import { evaluate } from 'mathjs'
import {ref} from 'vue'

export default{
  setup(){
    const botoes = [7, 8, 9, "%", 4, 5, 6, "+", 1, 2, 3, "-", ".", 0, "/", "*"]

    const naoNumero = valor => isNaN(valor)

    const atual = ref('')

    const acumulador = ref('')

    const resultado = ref('')

    const operadorClick = ref(true)

    const btnAction = valor => {
      if(!naoNumero(valor)){
        if(operadorClick.value){
          atual.value = ""
          operadorClick.value = false
        }

        atual.value = `${atual.value}${valor}`
      } else{
        executarOperaçao(valor)
      }
    }

    const executarOperaçao = valor => {
      if(valor === "."){
        if(atual.value.indexOf('.') === -1){
          atual.value = `${atual.value}${valor}`
        }
        return
      }

      if(valor === "%"){
        if(atual.value !== ''){
          atual.value = `${parseFloat(atual.value) / 100}`
        }
        return
      }
      acrescentaOperador(valor)
    }
    const acrescentaOperador = valor => {
      if (!operadorClick.value){
        acumulador.value += `${atual.value} ${valor} `
        atual.value = ""
        operadorClick.value = true
      }
    }

    const btnReset = () => {
      atual.value = ""
      acumulador.value = ""
      resultado.value = ""
      operadorClick.value = true
    }


    const btnResultado = () => {
      if(!operadorClick.value){
        resultado.value = evaluate(acumulador.value + atual.value)
      }else{
        resultado.value = "Error!"
      }
    }

    return {
      botoes,
      naoNumero,
      btnAction,
      atual,
      acumulador,
      btnReset,
      btnResultado,
      resultado


    }
  },
}


</script>

<style scoped>

.text-h5{
  height: 23px;
}

.text-h3{
  height: 50px;
}

</style>