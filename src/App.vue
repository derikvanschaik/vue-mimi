<template>
    <canvas :width="width" :height="height" ref="canvas"></canvas>
    <div v-for="t in textboxes" :key="t.idx">
        <drag-text :updateLinesHandler="updateLines" :startX="10" :startY="400"/>
    </div>
</template>

<script>
import DragText from './components/DragText.vue';

export default {
    components:{DragText},
    // TODO: maybe move this canvas resizing into a specific canvas component since it is not 
    // not really app logic...
    mounted(){
        this.clearCanvasAndDrawLines();
    },
    data(){
        return{
            // canvas
            width: window.innerWidth,
            height: window.innerHeight,
            // app 
            textboxes: [
                { id: 1 }
            ],
            lines: [ 
                { fromX: 10, fromY: 400, toX: 10, toY: 0},
            ],

        }
    },
    methods: {
        clearCanvasAndDrawLines(){
            const ctx = this.$refs.canvas.getContext("2d");
            ctx.clearRect(0, 0, this.width, this.height);

            this.lines.forEach( line =>{
                ctx.beginPath();
                ctx.moveTo(line.fromX, line.fromY);
                ctx.lineTo(line.toX, line.toY);
                ctx.stroke();
            })
        },
        // so far only redraws -- may need to add and delete lines in the future as well
        // maybe we can add a final param that is a string describing the action 
        // like: updateLines(oldX, oldY, newX, newY, action="DELETE")
        // or we can update and check the param count..
        updateLines(oldX, oldY, newX, newY){
            for(let i = 0; i < this.lines.length; i++){
                const {fromX, fromY, toX, toY} = this.lines[i];

                if(fromX === oldX && fromY === oldY){
                    this.lines[i] = {fromX: newX, fromY: newY, toX, toY};

                }else if (toX === oldX && toY === oldY){
                    this.lines[i] = {fromX, fromY, toX: newX, toY: newY};
                }
            }
            this.clearCanvasAndDrawLines();
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