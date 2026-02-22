<script setup>
    import { ref } from 'vue'
    import Alerta from './UI/Alerta.vue'

    const presupuesto = ref(0)
    const error = ref('')

    const emit = defineEmits([
        'presupuesto-definido'
    ])

    const definirPresupuesto = () => {
        if(presupuesto.value <= 0 || presupuesto.value === ''){
            error.value = 'El presupuesto no es válido'
            
            setTimeout(() => {
                error.value = ''
                presupuesto.value = 0
            }, 2000);
            return
        }
        emit('presupuesto-definido', presupuesto.value)
    }
</script>

<template>
  
  <div class="relative mx-5 flex flex-col items-center justify-center overflow-hidden rounded-xl p-8">
    
    <form @submit.prevent="definirPresupuesto" class="w-full flex flex-col gap-5 relative z-10">

      <Alerta v-if="error">{{ error }}</Alerta>

      <div class="flex flex-col gap-6 w-full">
        <label for="nuevo-presupuesto" class="text-2xl text-center text-black/50 font-bold">
          Definir Presupuesto
        </label>

        <input 
          id="nuevo-presupuesto"
          placeholder="Introduce tu presupuesto"
          type="number"
          v-model.number="presupuesto"
          class="bg-black/10 backdrop-blur-md rounded-lg border-0 text-2xl text-center p-3 focus:outline-none focus:ring-2 focus:ring-(--rosa)"
        />
      </div>

      <input 
        type="submit"
        value="Añadir presupuesto"
        class="py-5 bg-(--rosa) hover:bg-pink-700 transition-colors border-0 text-2xl text-white w-full font-bold rounded-lg cursor-pointer shadow-lg"
      />

    </form>
  </div>
</template>
