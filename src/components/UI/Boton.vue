<script setup>

</script>

<template>
    <button 
      class="
        cursor-pointer 
        bg-(--rosa) text-white 
        py-3 px-5 rounded-full text-3xl font-extrabold 
        hover:bg-white hover:text-(--rosa) hover:border-(--rosa) 
        transition-all duration-300 ease-in-out
      ">
        <slot></slot>
    </button>
</template>

