<script setup lang="ts">
import DarkMode from './DarkMode.vue'
import HeaderMenu from './HeaderMenu.vue'
</script>

<template>
    <header class="w-full fixed top-0 flex items-center justify-center backdrop-blur z-10">
        <div class="w-horizontal-root px-horizontal-padding py-6 flex items-center justify-between">
            <a href="/">
                <h4>template</h4>
            </a>
            <div class="flex gap-2">
                <DarkMode />
                <HeaderMenu />
            </div>
        </div>
    </header>
</template>
