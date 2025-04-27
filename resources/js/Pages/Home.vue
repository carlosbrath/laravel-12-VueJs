<script setup>
import { Head, Link } from '@inertiajs/vue3';

defineProps({
    canLogin: Boolean,
    canRegister: Boolean,
});
</script>

<template>
    <Head title="Welcome to RADOSOL" />

    <div class="bg-gray-50 dark:bg-black min-h-screen flex flex-col items-center justify-center text-black/80 dark:text-white/80">
        <div class="text-center px-6">
            <!-- Company Logo -->
            <img src="/images/logo.png" alt="RADOSOL Logo"  width="200" class="mx-auto mb-6 w-24 h-24 object-contain" />

            <!-- Company Name -->
            <h1 class="text-4xl font-bold mb-2">Welcome to <span class="text-red-600">RAD</span>O<span class="text-red-600">SOL</span></h1>

            <!-- Developer Info -->
            <p class="mt-4 text-lg">Crafted with passion by <strong>Ahsan Danish</strong></p>

            <!-- Some Introduction -->
            <p class="mt-2 text-gray-600 dark:text-gray-400">
                We build innovative software solutions tailored for your business needs.
            </p>

            <!-- Buttons -->
            <div class="mt-8 flex justify-center gap-4">
                <Link
                    v-if="canLogin"
                    :href="route('login')"
                    class="bg-red-600 text-white px-4 py-2 rounded-md hover:bg-red-700 transition"
                >
                    Login
                </Link>

                <Link
                    v-if="canRegister"
                    :href="route('register')"
                    class="border border-red-600 text-red-600 px-4 py-2 rounded-md hover:bg-red-600 hover:text-white transition"
                >
                    Register
                </Link>
            </div>
        </div>
    </div>
</template>
