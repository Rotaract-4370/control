<script setup>
import { ref, computed } from 'vue';
import Boton from './UI/Boton.vue';
import { opciones } from '../helpers';
import Alerta from './UI/Alerta.vue';

const error = ref('');

const emit = defineEmits([
    'cerrar-modal', 'guardar-gasto', 'eliminar-gasto',
    'update:nombre', 'update:cantidad', 'update:categoria'
]);

const props = defineProps({
    modal: { type: Object, required: true },
    nombre: { type: String, required: true },
    cantidad: { type: [Number, String], required: true },
    categoria: { type: String, required: true },
    disponible: { type: Number, required: true },
    id: { type: [String, null], default: null }
});

const cantidadOld = props.cantidad;
const isEditing = computed(() => !!props.id);

const mostrarError = (mensaje) => {
    error.value = mensaje;
    setTimeout(() => error.value = '', 3000);
};

const agregarGasto = () => {
    const { nombre, cantidad, categoria, disponible, id } = props;

    if ([nombre, categoria].includes('') || cantidad <= 0) {
        return mostrarError('Todos los campos son obligatorios y la cantidad debe ser mayor a 0');
    }

    const limiteEfectivo = id ? (cantidadOld + disponible) : disponible;
    
    if (cantidad > limiteEfectivo) {
        return mostrarError('Ese gasto supera tu presupuesto disponible');
    }

    emit('guardar-gasto');
    emit('cerrar-modal');
};
</script>

<template>
    <div class="fixed inset-0 bg-black/80 backdrop-blur-sm z-50 flex flex-col items-center justify-center transition-all duration-300">
        
        <div 
            class="absolute top-6 right-6 hover:rotate-90 transition-transform duration-300 cursor-pointer bg-white/10 p-2 rounded-full"
            @click="$emit('cerrar-modal')"
        >
            <i class="bi bi-x-lg text-4xl"></i>
        </div>

        <div 
            :class="[
                'w-11/12 md:w-125 bg-white/90 backdrop-blur-xl p-6 sm:p-8 md:p-10 rounded-2xl shadow-2xl transform transition-all duration-500',
                modal.animar ? 'translate-y-0 opacity-100' : 'translate-y-10 opacity-0'
            ]"
        >
            <h1 class="text-3xl font-black text-slate-800 mb-8 tracking-tight">
                {{ isEditing ? 'Editar Gasto' : 'Nuevo Gasto' }}
                <span class="block w-12 h-1 bg-blue-600 mt-2 rounded-full"></span>
            </h1>

            <Alerta v-if="error" class="mb-6">{{ error }}</Alerta>

            <form class="space-y-6" @submit.prevent="agregarGasto">
                
                <div class="flex flex-col text-left gap-2">
                    <label for="nombre" class="text-sm font-bold uppercase text-slate-500">¿En qué gastaste?</label>
                    <input 
                        type="text" id="nombre"
                        placeholder="Ej. Transporte, Comida..."
                        :value="nombre"
                        @input="$emit('update:nombre', $event.target.value)"
                        class="w-full p-4 bg-slate-100 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none transition-all border-none"
                    />
                </div>

                <div class="flex flex-col text-left gap-2">
                    <label for="categoria" class="text-sm font-bold uppercase text-slate-500">Categoría</label>
                    <select 
                        id="categoria"
                        :value="categoria"
                        @input="$emit('update:categoria', $event.target.value)"
                        class="w-full p-4 bg-slate-100 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none cursor-pointer border-none"
                    >
                        <option value="" disabled>-- Seleccione una categoría --</option>
                        <option v-for="(value, key) in opciones" :key="key" :value="key">{{ value }}</option>
                    </select>
                </div>

                <div class="flex flex-col text-left gap-2">
                    <label for="cantidad" class="text-sm font-bold uppercase text-slate-500">Monto</label>
                    <input 
                        type="number" id="cantidad"
                        placeholder="0.00"
                        :value="cantidad"
                        @input="$emit('update:cantidad', +$event.target.value)"
                        class="w-full p-4 bg-slate-100 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none text-xl font-semibold border-none"
                    />
                </div>

                <div class="flex flex-col gap-3 pt-4">
                    <Boton 
                        type="submit"
                        class="bg-blue-600! text-white! py-4! text-lg! !rounded-xl! shadow-blue-200! shadow-lg! hover:scale-[1.02] active:scale-95"
                    >
                        {{ isEditing ? 'Guardar Cambios' : 'Añadir Gasto' }}
                    </Boton>

                    <Boton 
                        v-if="isEditing" 
                        type="button"
                        @click="$emit('eliminar-gasto', id)" 
                        class="bg-transparent! text-red-500! py-2! hover:bg-red-50 rounded-xl! transition-colors"
                    >
                        Eliminar este gasto
                    </Boton>
                </div>
            </form>
        </div>
    </div>
</template>
