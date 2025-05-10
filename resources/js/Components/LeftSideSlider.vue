<script setup>
import { ref, onMounted } from 'vue'
const slides = [
  {
    image: '/images/originals/city.jpg',
    title: 'Perfect Balance',
    description: 'ArchitectUI is like a dream. Extensive collection of unified components & styles.'
  },
  {
    image: '/images/originals/citynights.jpg',
    title: 'Night Vibes',
    description: 'Experience the tranquility of the city under the stars with smooth performance.'
  },
  {
    image: '/images/originals/citydark.jpg',
    title: 'Dark Elegance',
    description: 'Sleek, modern, and powerful — designed for developers who demand the best.'
  }
]
const currentSlide = ref(slides[0])

onMounted(() => {
  let i = 0
  setInterval(() => {
    i = (i + 1) % slides.length
    currentSlide.value = slides[i]
  }, 4000)
})
</script>
<template>
<div class="hidden lg:flex w-1/2 flex-col justify-center relative overflow-hidden bg-gradient-to-br from-purple-700 to-indigo-900 text-white">
            <div
                class="absolute inset-0 bg-cover bg-center opacity-50 transition-all duration-1000"
                :style="{ backgroundImage: `url(${currentSlide.image})` }"
            ></div>
            <div class="relative z-10 px-12">
                <h2 class="text-3xl font-bold mb-4">{{currentSlide.title}}</h2>
                <p class="text-lg">
                {{ currentSlide.description }}
                </p>
            </div>
        </div>
</template>