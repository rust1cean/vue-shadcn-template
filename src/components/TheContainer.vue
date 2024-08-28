<template>
    <div
        class="w-horizontal-root px-horizontal-padding py-vertical-padding min-h-screen flex flex-col gap-8"
    >
        <header>
            <slot name="header"></slot>
        </header>
        <slot></slot>
        <footer>
            <slot name="footer"></slot>
        </footer>
    </div>
</template>
