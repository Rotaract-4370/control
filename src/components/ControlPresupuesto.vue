<script setup>
    import { ref, reactive, watch, computed, onMounted } from 'vue';
    import CircleProgress from 'vue3-circle-progress';
    import "vue3-circle-progress/dist/circle-progress.css";
    import { formatearCadena } from '../helpers';
    import Boton from './UI/Boton.vue';
    import gsap from 'gsap';

    const emit = defineEmits(['recetear-app']);

    const props = defineProps({
        totalGasto: { type: Number, required: true },
        totalAhorro: { type: Number, required: true },
        presupuesto: { type: Number, required: true },
        disponible: { type: Number, required: true }
    });

    const presupuestoAnimado = reactive({ valor: 0 });

    onMounted(() => {
        presupuestoAnimado.valor = props.disponible;
        
    });

    watch(() => props.disponible, (nuevoValor) => {
      gsap.to(presupuestoAnimado, {
        duration: 2,
        valor: nuevoValor,
        roundProps: "valor", 
        ease: "power3.out"
      });
    });

    const porcentaje = computed(() =>{
        if (props.presupuesto === 0) return 0;
        return Math.floor(((props.presupuesto - props.disponible) / props.presupuesto) * 100);
    });
</script>

<template>
    <div class="flex flex-col md:flex-row md:items-center gap-8 md:gap-12">
        <div class="relative flex items-center justify-center w-auto rounded-full md:bg-gray-200/50">
            <CircleProgress
                :percent="porcentaje"
                :size="200"
                :border-width="25"
                :border-bg-width="20"
                fill-color="#e1e1e1"
                empty-color="white"
            />
            <p class="absolute z-20 text-5xl font-extrabold text-(--rosa)">{{ porcentaje > 0 ? `${porcentaje}%` : '0%' }}</p>
        </div>

        <div class="w-full flex flex-col items-start gap-5">
            <Boton 
                class="
                  bg-red-600 border-red-700/20 text-white
                   border-r-2 border-b-2 
                   py-4 px-6 rounded-lg w-full hover:text-red-600
                   font-bold uppercase hover:bg-red-700 hover:border-gray-800/20"
                @click="emir('recetear-app')"
            >Resetear App
            </Boton>
            <div class="w-full flex flex-col items-start bg-gray-100 border-r-4 border-b-4 border-gray-200/20 rounded-2xl px-6 py-5 gap-5">
              <p class="text-ms md:text-2xl text-left text-gray-600"><span class="font-bold text-(--rosa)">Presupuesto: </span>{{ formatearCadena(presupuesto) }}</p>
              <p class="text-ms md:text-2xl text-left text-gray-600"><span class="font-bold text-(--rosa)">Disponible: </span>{{ formatearCadena(presupuestoAnimado.valor) }}</p>
              <p class="text-ms md:text-2xl text-left text-gray-600"><span class="font-bold text-(--rosa)">Gastos: </span>{{ formatearCadena(totalGasto) }}</p>
              <p class="text-ms md:text-2xl text-left text-gray-600"><span class="font-bold text-(--rosa)">Ahorros: </span>{{ formatearCadena(totalAhorro) }}</p>
            </div>
        </div>
    </div>
</template>

