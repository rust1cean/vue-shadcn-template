<script setup lang="ts">
import { onMounted, ref, watchEffect } from 'vue'
import { useWindowSize } from '@vueuse/core'

const props = withDefaults(
    defineProps<{
        particlesCount?: number
        borders?: () => {
            left: number
            top: number
            right: number
            bottom: number
        }
        particleRadius?: number
        particleFillColor?: string
        particleStrokeColor?: string
        minParticleVelocity?: () => { x: number; y: number }
        maxParticleVelocity?: () => { x: number; y: number }
    }>(),
    {
        particlesCount: 500,
        borders: () => ({
            left: 0,
            top: 0,
            right: document.documentElement.clientWidth,
            bottom: document.documentElement.clientHeight
        }),
        particleRadius:
            (document.documentElement.clientWidth + document.documentElement.clientHeight) /
            10 /
            1000,
        particleFillColor: 'gray',
        particleStrokeColor: 'transparent',
        minParticleVelocity: () => ({
            x: document.documentElement.clientWidth / 1_000,
            y: document.documentElement.clientHeight / 1_000
        }),
        maxParticleVelocity: () => ({
            x: document.documentElement.clientWidth / 250,
            y: document.documentElement.clientHeight / 250
        })
    }
)

const canvas = ref<HTMLCanvasElement | null>(null)

onMounted(() => {
    const windowSize = useWindowSize({ includeScrollbar: false })
    const ctx = canvas.value?.getContext('2d')

    // Resize canvas whenever window size changes
    watchEffect(() => {
        if (canvas.value != null) {
            canvas.value.width = windowSize.width.value
            canvas.value.height = windowSize.height.value
        }
    })

    if (ctx) {
        class Particle {
            ctx: CanvasRenderingContext2D
            position: { x: number; y: number }
            radius: number
            velocity: { x: number; y: number }
            fillColor: string
            strokeColor: string
            borders: { left: number; top: number; right: number; bottom: number }

            constructor(
                ctx: CanvasRenderingContext2D,
                {
                    position = {
                        x: windowSize.width.value / 2,
                        y: windowSize.height.value / 2
                    },
                    velocity = randomVelocity(),
                    radius = props.particleRadius,
                    fillColor = props.particleFillColor,
                    strokeColor = props.particleStrokeColor,
                    borders = props.borders()
                } = {}
            ) {
                this.ctx = ctx
                this.position = position
                this.radius = radius
                this.velocity = velocity
                this.fillColor = fillColor
                this.strokeColor = strokeColor
                this.borders = borders
            }
        }

        let particles: Particle[] = []

        const animation = () => {
            ctx.clearRect(0, 0, windowSize.width.value, windowSize.height.value)

            // Add particles when needed
            const notEnoughParticles = props.particlesCount - particles.length
            if (notEnoughParticles > 0) particles.push(new Particle(ctx))

            particles = particles.filter(lifecycle)

            requestAnimationFrame(animation)
        }

        const randomNumberInRange = (min: number, max: number) => Math.random() * (max - min) + min

        // Returns a boolean value that indicates the continuation of the particle's life cycle.
        const lifecycle = (particle: Particle) => {
            draw(particle)
            update(particle)

            return canLive(particle)
        }

        const canLive = ({
            position: { x, y },
            borders: { left, top, right, bottom }
        }: {
            position: { x: number; y: number }
            borders: {
                left: number
                top: number
                right: number
                bottom: number
            }
        }) => {
            return x >= left && x <= right && y >= top && y <= bottom
        }

        const draw = ({
            ctx,
            position: { x, y },
            radius,
            fillColor
        }: {
            ctx: CanvasRenderingContext2D
            position: { x: number; y: number }
            radius: number
            fillColor: string
        }) => {
            ctx.beginPath()
            ctx.fillStyle = fillColor
            ctx.arc(x, y, radius, 0, 2 * Math.PI)
            ctx.fill()
        }

        const update = (particle: Particle) => {
            particle.position.x += particle.velocity.x
            particle.position.y += particle.velocity.y

            particle.radius += Math.sqrt(particle.radius) / 100
        }

        const randomVelocity = () => {
            const minParticleVelocity = props.minParticleVelocity()
            const maxParticleVelocity = props.maxParticleVelocity()

            const absX = randomNumberInRange(minParticleVelocity.x, maxParticleVelocity.x)
            const absY = randomNumberInRange(minParticleVelocity.y, maxParticleVelocity.y)

            return {
                x: Math.random() > 0.5 ? -absX : absX,
                y: Math.random() > 0.5 ? -absY : absY
            }
        }

        requestAnimationFrame(animation)
    }
})
</script>

<template>
    <canvas class="absolute top-0 left-0 z-[-1]" ref="canvas"></canvas>
</template>
