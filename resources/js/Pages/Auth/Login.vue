<script setup>
import LeftSideSlider from '@/Components/LeftSideSlider.vue';
import NewGuestLayout from '@/Layouts/NewGuestLayout.vue';
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import Checkbox from '@/Components/Checkbox.vue'
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';


defineProps({
    canResetPassword: {
        type: Boolean,
    },
    status: {
        type: String,
    },
});

const form = useForm({
    email: '',
    password: '',
    remember: false,
});

const submit = () => {
    form.post(route('login'), {
        onFinish: () => form.reset('password'),
    });
};
</script>
<template>
    <div class="flex min-h-screen">
      <!-- Left side slider -->
        <LeftSideSlider />
  
      <!-- Right side form -->
      <div class="w-full lg:w flex items-center justify-center bg-gray-100">
        <NewGuestLayout>
          <Head title="Log in" />
  
          <div v-if="status" class="mb-4 text-sm font-medium text-green-600">
            {{ status }}
          </div>
  
          <form @submit.prevent="submit" class="space-y-6">
            <div>
              <InputLabel for="email" value="Email" />
              <TextInput
                id="email"
                type="email"
                class="mt-1 block w-full"
                v-model="form.email"
                required
                autofocus
                autocomplete="username"
              />
              <InputError class="mt-2" :message="form.errors.email" />
            </div>
  
            <div>
              <InputLabel for="password" value="Password" />
              <TextInput
                id="password"
                type="password"
                class="mt-1 block w-full"
                v-model="form.password"
                required
                autocomplete="current-password"
              />
              <InputError class="mt-2" :message="form.errors.password" />
            </div>
  
            <div class="flex items-center justify-between">
              <label class="flex items-center">
                <Checkbox name="remember" v-model:checked="form.remember" />
                <span class="ml-2 text-sm text-gray-600">Remember me</span>
              </label>
  
              <Link
                v-if="canResetPassword"
                :href="route('password.request')"
                class="text-sm text-indigo-600 hover:underline"
              >
                Forgot your password?
              </Link>
            </div>
  
            <div class="flex justify-end">
              <PrimaryButton
                :class="{ 'opacity-25': form.processing }"
                :disabled="form.processing"
              >
                Log in
              </PrimaryButton>
            </div>
          </form>
        </NewGuestLayout>
      </div>
    </div>
  </template>
  