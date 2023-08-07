<template>
    <div>
        <Suspense>
            <AsyncCityView />
            <template #fallback>
                <p class="text-center text-white py-24">Loading...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';
</script>