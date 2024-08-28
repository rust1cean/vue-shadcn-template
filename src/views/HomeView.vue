<script setup lang="ts">
import Autoplay from 'embla-carousel-autoplay'

import {
    Carousel,
    CarouselContent,
    CarouselItem,
    CarouselNext,
    CarouselPrevious
} from '@/components/ui/carousel'
import BackgroundAnimation from '@/components/BackgroundAnimation.vue'
import TheContainer from '@/components/TheContainer.vue'
import TheCalculator from '@/components/TheCalculator.vue'
</script>

<template>
    <main class="flex flex-col items-center justify-center">
        <TheContainer class="items-center justify-center">
            <Carousel
                :opts="{
                    loop: true
                }"
                :plugins="[
                    Autoplay({
                        delay: 5000
                    })
                ]"
            >
                <CarouselContent>
                    <CarouselItem class="flex items-center justify-center">
                        <h1 class="text-center leading-snug">
                            This is a Vue + shadcn-vue template!⚡🔥
                        </h1>
                    </CarouselItem>
                    <CarouselItem class="flex items-center justify-center">
                        <img
                            src="https://i.pinimg.com/736x/15/41/3d/15413d7f64049403cd40ac22473055dc.jpg"
                            alt="Cat"
                            class="rounded-lg"
                        />
                    </CarouselItem>
                </CarouselContent>
                <CarouselPrevious />
                <CarouselNext />
            </Carousel>
        </TheContainer>

        <TheContainer>
            <template v-slot:header>
                <h3>Calculator</h3>
            </template>
            <TheCalculator />
        </TheContainer>
        <BackgroundAnimation />
    </main>
</template>
