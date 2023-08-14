<template>
    <div class="flex flex-col flex-1">

        <!-- Overview -->
        <div class="flex flex-col py-12 items-center">
            <AnimatedPlaceholder class="max-w-[150px] w-full mb-2" />
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-12" />
            <AnimatedPlaceholder class="max-w-[300px] h-[75px] w-full mb-12"/>
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-2" />
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-3"/>
            <AnimatedPlaceholder class="max-w-[100px] max-h-[100px] w-full mb-3" />
            <AnimatedPlaceholder class="max-w-[300px] w-full mb-4"/>
        </div>

        <!-- More Info-->
        <div class="flex flex-col py-8 px-8 items-center">
            <AnimatedPlaceholder class="max-w-screen-md h-[100px] w-full mb-8"/>
        </div>

        <!-- Hourly -->
        <div class="flex flex-col py-8 px-8 items-center">
            <AnimatedPlaceholder class="max-w-screen-md h-[200px] w-full mb-8" />
        </div>

        <!-- Daily -->
        <div class="flex flex-col py-8 px-8 items-center">
            <AnimatedPlaceholder class="max-w-screen-md h-[150px] w-full mb-8"/>
        </div>
  </div>
</template>

<script setup>
import AnimatedPlaceholder from './AnimatedPlaceholder.vue';
</script>