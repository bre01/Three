<script>
import { time } from 'console';
import throttle from '../functions/throttle';
export default {
    data() {
        return {
            position: {
                x: 0,
                y: 0
            }
        }
    },
    mounted() {



        const throttledSet = throttle(() => {
            this.position.x= time.now()%100;
            this.position.y= time.now()%100;
            console.log(this.position);
        }, 1000)
        function animate() {
            throttledSet();
            requestAnimationFrame(animate);
        }
        requestAnimationFrame(animate);


    }

}
</script>
<template>
    <Mapdiv :position="position"></Mapdiv>
</template>