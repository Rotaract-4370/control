<script setup>
import { ref, watch, onMounted } from 'vue';
import gsap from 'gsap';

const props = defineProps({
  presupuesto: { type: Number, required: true }
});

// Referencia al contenedor para aplicar los efectos de GSAP
const videoContainer = ref(null);

watch(() => props.presupuesto, (nuevoPresupuesto) => {
  if (nuevoPresupuesto > 0) {
    // Aplicamos el efecto al DIV contenedor
    gsap.to(videoContainer.value, {
      filter: "blur(8px) brightness(0.6)",
      scale: 1.05, // Un pequeño zoom para darle dinamismo
      duration: 1.5,
      ease: "power2.out"
    });
  } else {
    gsap.to(videoContainer.value, {
      filter: "blur(0px) brightness(1)",
      scale: 1,
      duration: 1,
      ease: "power2.inOut"
    });
  }
});
</script>

<template>
  <div 
    ref="videoContainer"
    class="video-background-wrapper"
  >
    <div class="aspect-ratio-box">
      <iframe 
        src="https://player.vimeo.com/video/1167115199?badge=0&autopause=0&player_id=0&app_id=58479&autoplay=1&loop=1&background=1&muted=1" 
        frameborder="0" 
        allow="autoplay; fullscreen; picture-in-picture" 
        class="vimeo-iframe"
        title="bucle fondo"
      ></iframe>
    </div>
    
    <div class="overlay"></div>
  </div>
</template>

<style scoped>
.video-background-wrapper {
  position: fixed; /* O absolute, dependiendo de tu layout */
  top: 0;
  left: 0;
  width: 100%;
  height: 100vh;
  z-index: -1; /* Se mantiene al fondo */
  overflow: hidden;
  background-color: black;
}

.aspect-ratio-box {
  padding: 56.25% 0 0 0;
  position: relative;
  height: 100%;
}

.vimeo-iframe {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100vw;
  height: 100vh;
  transform: translate(-50%, -50%);
  object-fit: cover;
  pointer-events: none; /* Importante para que no interfiera con los clicks de la App */
}

/* Si tu monitor es ultra-wide, esto asegura que el video cubra todo */
@media (min-aspect-ratio: 16/9) {
  .vimeo-iframe {
    height: 56.25vw;
  }
}
@media (max-aspect-ratio: 16/9) {
  .vimeo-iframe {
    width: 177.78vh;
  }
}

.overlay {
  position: absolute;
  inset: 0;
  background: rgba(0, 0, 0, 0.2);
  z-index: 1;
}
</style>
