<template>
    <div id="mainParent">
        <canvas ref="canvas" id="three" @dblclick="onClick"></canvas>

        <ShowCanvas v-if="canvasClickedFlag" :thing-to-show="clone"></ShowCanvas>
    </div>
</template>


<script lang="ts" setup>
import { FlyControls } from 'three/examples/jsm/controls/FlyControls.js';

import * as THREE from "three";
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { ref } from 'vue';

const scene: THREE.Scene = new THREE.Scene(); 
const camera: THREE.Camera = new THREE.PerspectiveCamera(75, 1, 0.1, 1000)
camera.position.set(50, 50, 50);
const light: THREE.AmbientLight = new THREE.AmbientLight(0x999999, 5);
const loader: GLTFLoader = new GLTFLoader()
//controls.target.set(0, 0, 0);
const allObjects:THREE.Group<THREE.Object3DEventMap>[] = [];






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
        gltf.scene.position.set(80,80,0)

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
let thingToShow:THREE.Group<THREE.Object3DEventMap>|{}=reactive({});


    const animationPosition = ref({ x: 0, y: 0 });
    const canvasRef = ref(null);





const canvasClickedFlag = ref(false);
let clone: THREE.Group<THREE.Object3DEventMap>|{}=reactive({});
/*
function canvasClicked() {
    canvasClickedFlag.value = true;
    clone = Object.create(
        Object.getPrototypeOf(thingToShow), Object.getOwnPropertyDescriptors(thingToShow)
    );
}*/
const raycaster=new THREE.Raycaster();
function onClick(event: MouseEvent) {
    // Handle click event
    // ...
    const canvas=document.getElementById("three") as HTMLCanvasElement;
    const rect = canvas.getBoundingClientRect();

      const x = event.clientX - rect.left;
      const y = event.clientY - rect.top;

      // Set the animation position
      animationPosition.value = { x, y };


    canvasClickedFlag.value = true;
  
    var mouse = new THREE.Vector2(
        (event.clientX / window.innerWidth) * 2 - 1,
        -(event.clientY / window.innerHeight) * 2 + 1,
    )
    console.log('clicked')

    raycaster.setFromCamera(mouse, camera)
    console.log(allObjects)
    var intersects = raycaster.intersectObjects(allObjects)//array
    if (intersects.length > 0) {
        thingToShow= intersects[0].object.parent?intersects[0].object.parent:intersects[0].object;
        //thingToShow= intersects[0].object;
        clone = Object.create(
        Object.getPrototypeOf(thingToShow), Object.getOwnPropertyDescriptors(thingToShow)
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
    else{
        canvasClickedFlag.value=false;
    }
}
onMounted(() => {

    const threeCanvas: HTMLCanvasElement = document.getElementById("three") as HTMLCanvasElement;
    const renderer: THREE.Renderer = new THREE.WebGLRenderer({ antialias: true, canvas: threeCanvas });
    const controls: OrbitControls = new OrbitControls(camera, renderer.domElement);
    const fly = new FlyControls(camera, threeCanvas);
    renderer.setSize(600, 600);
    function animate() {
        renderer.render(scene, camera);
        controls.update();
        fly.update(1)
        requestAnimationFrame(animate);
    }
    requestAnimationFrame(animate);
})
</script>
<style>
#mainParent {
    position: relative;
}

#three {
    width: 600px;
    height: 600px;
    background-color: aliceblue;
    position: relative;
}
</style>