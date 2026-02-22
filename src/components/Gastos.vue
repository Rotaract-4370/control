<script setup>
  import { formatearCantidad, formatearFecha } from '../helpers';

  const props = defineProps({
    gastos: { type: Array, required: true }
  });

  defineEmits(['seleccionar-gasto']);
</script>

<template>
  <div class="grid w-[90%] mx-auto grid-cols-1 md:grid-cols-2 gap-6 my-10">
    <div 
      v-for="gasto in gastos" 
      :key="gasto.id"
      @click="$emit('seleccionar-gasto', gasto.id)"
      class="group relative flex items-center justify-between gap-6 bg-white p-6 rounded-2xl shadow-sm border border-slate-100 transition-all hover:shadow-md hover:-translate-y-1 cursor-pointer overflow-hidden"
    >
      <div 
        :class="[
          'absolute left-0 top-0 h-full w-2 transition-all group-hover:w-3',
          gasto.categoria === 'ahorro' ? 'bg-emerald-500' : 'bg-rose-500'
        ]"
      ></div>

      <div class="flex items-center gap-6">
        <div class="hidden sm:block w-16 h-16 p-3 bg-slate-50 rounded-full border border-slate-100">
            <i 
              class="bi bi-cash-stack text-4xl text-slate-800"
            ></i>
        </div>

        <div class="space-y-1">
          <p class="text-xs font-bold uppercase tracking-wider text-slate-400">
            {{ gasto.categoria }}
          </p>
          <h2 class="text-xl font-black text-slate-800 leading-tight">
            {{ gasto.nombre }}
          </h2>
          <p class="text-sm text-slate-500 flex items-center gap-2">
            <span class="font-medium text-slate-400">Agregado el:</span>
            {{ formatearFecha(gasto.fecha) }}
          </p>
          <p class="text-xs font-mono text-slate-500 uppercase tracking-tighter">
            ID: {{ gasto.id.split('-')[0] }}
          </p>
        </div>
      </div>

      <div class="text-right">
        <p 
          :class="[
            'text-2xl font-black tracking-tight',
            gasto.categoria === 'ahorro' ? 'text-emerald-600' : 'text-slate-800'
          ]"
        >
          {{ gasto.categoria === 'ahorro' ? '+' : '-' }}{{ formatearCantidad(gasto.cantidad) }}
        </p>
      </div>
    </div>
  </div>
</template>
