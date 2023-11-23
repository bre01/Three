<script>
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

        const setTime=()=>{
            let d = new Date();
            this.position.x= d.getTime()%100;
            this.position.y= d.getTime()%100;
            console.log(this.position);
        }

        const throttledSet = throttle(() => {
            setTime();    
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
    <TestMap :position="position"></TestMap>
</template>