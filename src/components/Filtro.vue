<script setup>
    import { opciones } from '../helpers';

    defineProps(['filtro'])
    defineEmits(['update:filtro'])
</script>

<template> 
    <div class="flex flex-col items-center justify-between -mb-10 md:mt-15 bg-white rounded-xl shadow-lg p-6 w-[90%] mx-5 md:w-2/3 md:mx-auto">
        <form class="w-full">
            <div class="flex items-center w-full justify-between gap-5 md:gap-10">
                <label for="filtro" class="text-xl md:text-3xl font-black text-(--rosa) whitespace-nowrap">
                    Filtrar Gastos
                </label>
                
                <select 
                    id="categoria-formulario"
                    :value="filtro"
                    @input="$emit('update:filtro', $event.target.value)"
                    class="flex-1 p-3 border-0 rounded-lg bg-(--gris-claro) text-center outline-none cursor-pointer"
                >
                    <option value="">-- Seleccione --</option>
                    <option v-for="(value, key) in opciones" :key="key" :value="key">
                        {{ value }}
                    </option>
                </select>
            </div>
        </form>
    </div>
</template>
