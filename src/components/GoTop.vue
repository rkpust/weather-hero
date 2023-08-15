<template>
    <div class="flex flex-col bottom-0 mr-4 mt-3 items-end">
        <button
            class="text-6xl font-bold text-white px-2 hover:text-green-400 h-[60px] w-[60px]"
            @click="GoToTop"
            title="Go To Top"
        >
            <i class="fa-solid fa-arrow-up fa-bounce bg-weather-secondary rounded-full"></i>
        </button>
    </div>
</template>

<script setup>
const GoToTop = () => {
// When the user clicks on the button, scroll to the top of the document
 document.body.scrollTop = 0;
 document.documentElement.scrollTop = 0;
}
</script>