<template>
    <canvas :width="width" :height="height"></canvas>
    <div v-for="i in [1, 2, 3]" :key="i">
        <drag-text/>
    </div>
</template>

<script>
import DragText from './components/DragText.vue';

export default {
    components:{DragText},
    mounted(){
        this.width = window.innerWidth;
        this.height = window.innerHeight;
    }, 
    data(){
        return{
            width: 0,
            height: 0
        }
    }
}
</script>
<style>
*{
    margin: 0;
    padding:0
}
canvas{
    position: absolute;
}
</style>