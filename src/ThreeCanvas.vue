<template>
    <div ref="container" class="three-canvas-wrapper">
        <canvas ref="canvas" class="three-canvas" />
    </div>
</template>

<script>
import * as THREE from 'three'

export default {
    props: {
        fov: { type: Number, default: 75 },
        orthographic: { type: Boolean, default: false },
    },
    data() {
        return {
            resizeObserver: null,

            containerWidth: 1,
            containerHeight: 1,

            // set on this component, but not reacted to:
            // * renderer
            // * scene
            // * camera
        }
    },
    mounted() {
        // prep scene
        this.scene = new THREE.Scene()

        // prep camera
        if (this.orthographic) {
            const width =
                this.fov / (this.containerHeight / this.containerWidth)
            const height = this.fov
            const halfWidth = width / 2
            const halfHeight = height / 2
            this.camera = new THREE.OrthographicCamera(
                -halfWidth,
                halfWidth,
                -halfHeight,
                halfHeight,
                0.1,
                1000
            )
        } else {
            this.camera = new THREE.PerspectiveCamera(
                this.fov,
                0.5625,
                0.1,
                1000
            )
        }

        // prep base renderer
        this.renderer = new THREE.WebGLRenderer({
            canvas: this.$refs.canvas,
            antialias: true,
            alpha: true,
        })

        // update canvas size
        this.updateSize()

        // prep resize observer
        this.resizeObserver = new ResizeObserver(this.updateSize)
        this.resizeObserver.observe(this.$refs.container)

        // start
        this.$emit('start', {
            renderer: this.renderer,
            scene: this.scene,
            camera: this.camera,
        })

        // update
        this.update()
    },
    methods: {
        update() {
            if (this && this.update) {
                requestAnimationFrame(this.update)
            }

            this.$emit('update', {
                renderer: this.renderer,
                scene: this.scene,
                camera: this.camera,
            })

            this.renderer.render(this.scene, this.camera)
        },
        updateSize() {
            const { container } = this.$refs

            if (!this.camera || !this.renderer || !container) return

            this.containerWidth = container.offsetWidth
            this.containerHeight = container.offsetHeight

            if (this.orthographic) {
                const width =
                    this.fov / (this.containerHeight / this.containerWidth)
                const height = this.fov
                const halfWidth = width / 2
                const halfHeight = height / 2
                this.camera.left = -halfWidth
                this.camera.right = halfWidth
                this.camera.top = halfHeight
                this.camera.bottom = -halfHeight
            } else {
                // update aspect ratio, projection matrix, and renderer size
                this.camera.aspect = this.containerWidth / this.containerHeight
            }
            this.camera.updateProjectionMatrix()
            this.renderer.setSize(this.containerWidth, this.containerHeight)

            // render on resize to avoid flickering
            this.renderer.render(this.scene, this.camera)
        },
    },
}
</script>

<style lang="scss">
.three-canvas-wrapper,
.three-canvas {
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
</style>