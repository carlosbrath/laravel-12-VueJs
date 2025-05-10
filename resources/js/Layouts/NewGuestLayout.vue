<script setup>
import ApplicationLogo from '@/Components/ApplicationLogo.vue';
import { Link } from '@inertiajs/vue3';
</script>

<template>
    <div class="w-full max-w-md sm:max-w-lg md:max-w-xl px-6 py-8 bg-white shadow-md rounded-lg">
        <div class="flex justify-center mb-6">
            <Link href="/">
                <ApplicationLogo class="h-16 w-16 fill-current text-gray-500" />
            </Link>
        </div>
        <slot />
    </div>
</template>