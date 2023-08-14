<template>
    <div class="flex py-6 px-3 bg-gradient-to-bl from-weather-secondary to-weather-primary rounded-md shadow-md">
        <div class="flex flex-col flex-1 gap-2">
            <AnimatedPlaceholder class="max-w-[60px]" />
            <AnimatedPlaceholder class="max-w-[50px]" />
        </div>

        <div class="flex flex-col flex-1 items-end gap-2 ">
            <AnimatedPlaceholder class="max-w-[50px] w-full" />
            <AnimatedPlaceholder class="max-w-[75px] w-full" />
        </div>
    </div>
</template>

<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';
</script>