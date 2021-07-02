<template>
    <div id="app"><three-canvas @start="start" @update="update" /></div>
</template>

<script>
import ThreeCanvas from './ThreeCanvas.vue'
import { BoxGeometry, MeshBasicMaterial, Mesh } from 'three'

const ref = {}
export default {
    components: { ThreeCanvas },
    methods: {
        start({ scene }) {
            const geometry = new BoxGeometry()
            const material = new MeshBasicMaterial({
                color: 0xff0000,
            })
            ref.cube = new Mesh(geometry, material)
            ref.cube.position.z = -4

            scene.add(ref.cube)
        },
        update() {
            ref.cube.rotation.y -= 0.01
        },
    },
}
</script>

<style lang="scss">
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
