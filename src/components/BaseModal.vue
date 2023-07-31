<template>
    <div
        v-show="modalActive"
        class="absolute w-full bg-black bg-opacity-30 h-screen top-0 left-0 flex justify-center px-8"
    >
        <div
            v-if="modalActive"
            class="p-4 bg-white self-start mt-32 max-w-screen-md rounded-md"
        >
            <slot />
            <button class="text-white mt-8 bg-weather-primary py-2 px-6">Close</button>
        </div>
    </div>
</template>

<script setup>
defineProps({
    modalActive: {
        type: Boolean,
        default: false,
    },
});
</script>