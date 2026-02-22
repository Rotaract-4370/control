<script setup>

</script>

<template>
    <div 
      class="
        text-red-700 font-bold
        p-4 text-md uppercase 
        text-center -mb-4
        after:content-[''] md:after:left-15 after:left-0 after:top-0
        after:rounded-2xl after:absolute
        after:transition-all after:duration-300 after:ease-in-out
        after:w-1 after:h-10 after:bg-red-700
        ">
        <slot></slot>
    </div>
</template>

