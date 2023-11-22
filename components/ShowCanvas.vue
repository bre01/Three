<template>
    <div>
        <canvas id="show"></canvas>
    </div>
</template>
<script lang="ts" setup>

import { defineProps } from 'vue';
import * as THREE from "three";
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
const props = defineProps({
    thingToShow:THREE.Group<THREE.Object3DEventMap>
});


onMounted(() => {
    const threeCanvas: HTMLCanvasElement = document.getElementById("show") as HTMLCanvasElement;
    const scene: THREE.Scene = new THREE.Scene();
    const renderer: THREE.Renderer = new THREE.WebGLRenderer({ antialias: true, canvas: threeCanvas });
    const camera: THREE.Camera = new THREE.PerspectiveCamera(75, 1, 0.1, 1000)
    camera.position.set(50, 50, 50);
    const light: THREE.AmbientLight = new THREE.AmbientLight(0x999999, 5);
    const loader: GLTFLoader = new GLTFLoader()
    const controls: OrbitControls = new OrbitControls(camera, renderer.domElement);
    //controls.target.set(0, 0, 0);
    scene.add(props.thingToShow!);
    console.log("show"+props.thingToShow)
    renderer.setSize(200,200);
    const geometry = new THREE.BoxGeometry(1, 1, 1);
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    cube.position.set(20,20,20);
    scene.add(cube);
    scene.add(light);
    scene.add(camera);
    console.log("done");
    function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
    }
    animate();

})
</script>
<style>
#show {
    width: 200px;
    height: 200px;
    background-color: aliceblue;
    position: absolute;
    top: 100px;
    z-index: 100;

}
</style>