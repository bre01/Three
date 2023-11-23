<template>
    <TestMap :position="position"></TestMap>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import throttle from '../functions/throttle';

const position = ref({ x: 0, y: 0 });

const setTime = () => {
    let d = new Date();
    position.value.x = d.getTime() % 100;
    position.value.y = d.getTime() % 100;
    console.log(position.value);
};

const throttledSet = throttle(() => {
    setTime();
}, 1000);

onMounted(() => {
    function animate() {
        throttledSet();
        requestAnimationFrame(animate);
    }
    requestAnimationFrame(animate);
});

</script>

