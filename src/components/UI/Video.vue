<script setup>
import { ref, watch } from 'vue';
import gsap from 'gsap';
import video from '../../../public/bucle.mp4'


const props = defineProps({
  presupuesto: { type: Number, required: true }
});


const videoRef = ref(null);

watch(() => props.presupuesto, (nuevoPresupuesto) => {
  if (nuevoPresupuesto > 0) {
    gsap.to(videoRef.value, {
      filter: "blur(8px) brightness(0.7)",
      duration: 1.2,
      ease: "power2.out"
    });
  } else {
    // Volver al estado original
    gsap.to(videoRef.value, {
      filter: "blur(0px) brightness(1)",
      duration: 0.8,
      ease: "back.inOut"
    });
  }
});
</script>

<template>
  <video 
    ref="videoRef"
    autoplay muted loop playsinline
    class="absolute top-0 left-0 w-full h-full object-cover -z-10 pointer-events-none 
           mask-[linear-gradient(to_bottom,black_70%,transparent_100%)]"
  >
    <source :src="video" type="video/mp4">
  </video>
</template>
