<template>
    <div id="mainParent">
        <canvas id="three" @dblclick="onClick"></canvas>

        <ShowCanvas v-if="canvasClickedFlag" :thing-to-show="clone"></ShowCanvas>
        <div id="map"> </div>
        <!--
        <ClientOnly>

            <MapDiv :position="positionR"></MapDiv>
        </ClientOnly>
        -->
    </div>
</template>


<script lang="ts" setup>
import { FlyControls } from 'three/examples/jsm/controls/FlyControls.js';
import throttle from '../functions/throttle.js';
import * as THREE from "three";
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { ref } from 'vue';
import * as L from "leaflet";
import "leaflet/dist/leaflet.css"



const scene: THREE.Scene = new THREE.Scene();
const camera: THREE.Camera = new THREE.PerspectiveCamera(75, 1, 0.1, 1000)
camera.position.set(50, 50, 50);
const light: THREE.AmbientLight = new THREE.AmbientLight(0x999999, 5);
const loader: GLTFLoader = new GLTFLoader()
//controls.target.set(0, 0, 0);
const allObjects: THREE.Group<THREE.Object3DEventMap>[] = [];
let positionR: Ref<THREE.Vector | undefined> = ref();

loader.load(
    // resource URL
    'models/3.glb',
    // called when the resource is loaded
    function (gltf) {
        //gltf.scene.position.set(0, 0, 0)
        scene.add(gltf.scene);
        allObjects.push(gltf.scene);
    }, function (xhr) {

        console.log((xhr.loaded / xhr.total * 100) + '% loaded');

    },
    // called when loading has errors
    function (error) {

        console.log('An error happened');

    }
)
loader.load(
    // resource URL
    'models/test2.glb',
    // called when the resource is loaded
    function (gltf) {
        //gltf.scene.position.set(0, 0, 0)
        gltf.scene.position.set(50,50,30)
        scene.add(gltf.scene);
        allObjects.push(gltf.scene);
    }, function (xhr) {

        console.log((xhr.loaded / xhr.total * 100) + '% loaded');

    },
    // called when loading has errors
    function (error) {

        console.log('An error happened');

    }
)
const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
const cube = new THREE.Mesh(geometry, material);
//allObjects.push(cube);
cube.position.set(20, 20, 20);
scene.add(light);
scene.add(camera);
console.log("done");
let thingToShow = ref();









const canvasClickedFlag = ref(false);
let clone: THREE.Group<THREE.Object3DEventMap>;
/*
function canvasClicked() {
    canvasClickedFlag.value = true;
    clone = Object.create(
        Object.getPrototypeOf(thingToShow), Object.getOwnPropertyDescriptors(thingToShow)
    );
}*/
const raycaster = new THREE.Raycaster();
function onClick(event: MouseEvent) {
    // Handle click event
    // ...
    canvasClickedFlag.value = true;

    var mouse = new THREE.Vector2(
        (event.clientX / window.innerWidth) * 2 - 1,
        -(event.clientY / window.innerHeight) * 2 + 1,
    )
    console.log('clicked')

    raycaster.setFromCamera(mouse, camera)
    console.log(allObjects)
    var intersects = raycaster.intersectObjects(allObjects, true)//array
    if (intersects.length > 0) {
        thingToShow.value = intersects[0].object.parent
        clone = Object.create(
            Object.getPrototypeOf(thingToShow.value), Object.getOwnPropertyDescriptors(thingToShow.value)
        );
        console.log("there")
        console.log(thingToShow)
        /*
        target.value = [
            selectedObject.point.x,
            selectedObject.point.y,
            selectedObject.point.z,
        ]
        console.log(target)
        */
    }
}
let mak:L.Marker;

onMounted(() => {

    const threeCanvas: HTMLCanvasElement = document.getElementById("three") as HTMLCanvasElement;
    const renderer: THREE.Renderer = new THREE.WebGLRenderer({ antialias: true, canvas: threeCanvas });
    const controls: OrbitControls = new OrbitControls(camera, renderer.domElement);
    const fly = new FlyControls(camera, threeCanvas);
    renderer.setSize(600, 600);
    scene.background = new THREE.Color(0xfff8f0);
    const throttledSet = throttle(() => {
        positionR.value = camera.position;
        console.log(positionR.value);
        mak.setLatLng(L.latLng([camera.position.x+150,camera.position.y+150]))
        console.log(mak.getLatLng())
    }, 1000)


    //maps
    const map = L.map('map', {
        crs: L.CRS.Simple
    });
    const bounds = [[0, 0], [300, 300]] as L.LatLngBoundsLiteral;
    const image = L.imageOverlay("models/download.jpeg", bounds).addTo(map);
    map.fitBounds(bounds);
    const sol = L.latLng([150, 150]);
    mak = L.marker(sol)
    

    mak.addTo(map)







    function animate() {
        renderer.render(scene, camera);
        fly.update(1)
        controls.update()
        requestAnimationFrame(animate);
        throttledSet();
    }
    requestAnimationFrame(animate);
})
/*
watch(() => positionR, (newPosition) => {
    console.log("changed");
    console.log(newPosition.value!.x, newPosition.value!.y);
    if (mak) {
       mak.setLatLng([newPosition!.x, newPosition!.y]);
    }
})
*/
</script>

<style>
#mainParent {
    position: relative;
}

#three {
    width: 600px;
    height: 600px;
    background-color: rgb(255, 249, 240);
    position: relative;
}
#map {
   width: 300px;
   height: 300px;
   position: absolute;
   top: 20px;
   right: 0px;
   z-index: 100;
}
</style>