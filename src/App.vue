<script setup>
  import { ref, reactive, watch, computed, onMounted } from 'vue';
  import { generarID } from './helpers';
  import Presupuesto from './components/Presupuesto.vue';
  import ControlPresupuesto from './components/ControlPresupuesto.vue';
  import Gastos from './components/Gastos.vue';
  import Modal from './components/Modal.vue';
  import Filtro from './components/Filtro.vue';
  import Video from './components/UI/Video.vue'
  import logo from '../src/assets/logo.png'


  const presupuesto = ref(0);
  const disponible = ref(0);
  const totalGasto = ref(0);
  const totalAhorro = ref(0);
  const gastos = ref([]);
  const filtro = ref('');

  const modal = reactive({
    mostrar: false,
    animar: false
  });
  

  watch(
    gastos,
    (nuevoGastos) => {
      localStorage.setItem('gastos', JSON.stringify(gastos.value));
      const { totalGasto: tg, totalAhorro: ta } = nuevoGastos.reduce((acc, gasto) => {
          gasto.categoria === 'ahorro' ? 
          acc.totalAhorro += Number(gasto.cantidad) :
          acc.totalGasto += Number(gasto.cantidad);
          return acc;
        },{ totalGasto: 0, totalAhorro: 0 }
      );
      totalGasto.value = tg;
      totalAhorro.value = ta;
    },
    { deep: true }
  );
  watch([totalGasto, totalAhorro], () => {
        disponible.value = presupuesto.value - (totalGasto.value + totalAhorro.value)
  }, { deep: true})

  watch(modal, () => {
    if(!modal.mostrar){recetObjeto();}})

  watch([presupuesto, gastos], () => {
    localStorage.setItem('presupuesto', presupuesto.value);
  })

  onMounted(() => {
    const presupuestoStorage = localStorage.getItem('presupuesto');
    if(presupuestoStorage){
      presupuesto.value = Number(presupuestoStorage);
      disponible.value = Number(presupuestoStorage);

    }
    const gastosStorage = localStorage.getItem('gastos');
    if(localStorage.getItem('gastos')){
      gastos.value = JSON.parse(gastosStorage);
    }
  })

  const gasto = reactive({
    nombre: '',
    cantidad: 0,
    categoria: '',
    id: null,
    fecha: Date.now()
  })

  const recetearApp = () => {
    if(confirm('Estas seguro de resetear la app?')){
      presupuesto.value = 0;
      disponible.value = 0;
      gastos.value = [];
    }
  }

  const recetObjeto = () => {
    Object.assign(gasto, {
      nombre: '',
      cantidad: 0,
      categoria: '',
      id: null,
      fecha: Date.now()
    });
  };

  const guardarGasto = () => {
    if (gasto.id){
      const {id} = gasto;
      const i = gastos.value.findIndex(gasto => gasto.id === id);
      gastos.value[i] = {...gasto}
    }else{
      gastos.value.push({
      ...gasto,
      id:generarID(), 
    });
    }    
  }

  const eliminarGasto = (id) => {
    if (confirm('Estas seguro de eliminar el gasto?')){
      gastos.value = gastos.value.filter(gasto => gasto.id !== id);
    }
  }

  const definirPresupuesto = (cantidad) => {
    presupuesto.value = cantidad;
    disponible.value = disponible.value + cantidad;
  };

  const mostrarModal = () => {
    modal.mostrar= true;
    setTimeout(() => {
      modal.animar = true;
    }, 250);
  }

  const ocultarModal = () => {
    modal.animar = false;
    setTimeout(() => {
      modal.mostrar = false;
    }, 300);
  }

  
  // Seleccionar gastos / modificar gastos
  const seleccionarGasto = id => {
    const gastoEditar = gastos.value.filter(gasto => gasto.id === id)[0];
    mostrarModal();
    Object.assign(gasto, gastoEditar);
  }
  const gastosFiltrados = computed(() => {
    if(filtro.value){
      return gastos.value.filter(gasto => gasto.categoria === filtro.value)
    }
    return gastos.value
  })
  const cabeceraGastos = computed(() =>{
    if(gastosFiltrados.value.length === 0) return 'No hay gastos';
    return gastosFiltrados.value.length > 1 ? 
    `Tienes ${gastosFiltrados.value.length} gastos`:
    `Tienes ${gastosFiltrados.value.length} gasto`;
  })  
</script>

<template>
  <div :class="modal.mostrar ? 'overflow-hidden h-screen' : ''">
    <header class="bg-linear-to-b from-(--rosa)/70 to-transparent rounded-2xl m-5 pb-10">
      <!-- <img :src="logo" class="hidden md:block fixed w-30 h-30 left-30 top-20 p-3 rounded-full shadow-2xl border-4 border-(--rosa)" alt="logo"> -->
      <Video 
        :presupuesto="presupuesto"
      />
      <p class="text-white text-center py-10 md:15 text-2xl md:text-4xl font-black">Control de gastos</p>

      <div class="mx-auto w-full md:w-[90%] max-w-7xl md:-mt-25 transform md:translate-y-20 md:p-13 px-5 py-10 bg-linear-to-b from-white to-white/20 rounded-xl drop-shadow-2xl drop-shadow-zinc-800/50">
        <Presupuesto 
          v-if="presupuesto === 0"
          @presupuesto-definido="definirPresupuesto"
        />
        <ControlPresupuesto 
          v-else
          @recetear-app="recetearApp"
          :presupuesto="presupuesto"
          :disponible="disponible"
          :totalGasto="totalGasto"
          :totalAhorro="totalAhorro"
        />
      </div>
    </header>
    <main v-if="presupuesto > 0">
      <Filtro 
        v-if="gastos.length > 0"
        v-model:filtro="filtro"
      />
      <div class="mx-auto w-full max-w-7xl flex flex-col items-center gap-8">
        <p 
        class="
            mt-20 ml-8 -mb-10 text-3xl md:text-4xl 
            font-extrabold text-shadow-2xs 
            text-shadow-black text-gray-100"
        :class="{'text-pink-600 text-shadow-none' : gastosFiltrados.length > 0}"
        >{{cabeceraGastos}}</p>
        <Gastos 
          :gastos="gastosFiltrados"
          @seleccionar-gasto="seleccionarGasto"
        />
      </div>

      <div 
        class="
          fixed bottom-8 right-8 md:right-12 z-50
          font-extrabold
          w-16 h-16 bg-white/50 rounded-2xl
          flex items-center justify-center
          text-gray-500 shadow-2xl cursor-pointer
          hover:scale-110 hover:bg-white/50
          transition-all duration-300 ease-in-out"
        @click="mostrarModal"
      >
        <i class="bi bi-plus-lg text-3xl"></i>
      </div>
        
      <Modal 
        v-if="modal.mostrar"
        @cerrar-modal="ocultarModal"
        @guardar-gasto="guardarGasto"
        @eliminar-gasto="eliminarGasto"
        :modal="modal"
        :id="gasto.id"
        :disponible="disponible"
        v-model:nombre="gasto.nombre"
        v-model:cantidad="gasto.cantidad"
        v-model:categoria="gasto.categoria"
      />
    </main>
  </div>

</template>

