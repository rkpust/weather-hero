<template>
    <div>
        <Suspense>
            <AsyncCityView />
            <template #fallback>
                <p class="text-center text-white py-24">Loading...</p>
            </template>
        </Suspense>
        <h1>Hello From CityView</h1>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';
</script>