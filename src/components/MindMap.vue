<template>
    <canvas :width="width" :height="height" ref="canvas"></canvas>
    <button class="menu-btn close" @click="handleClose">Close</button>
    <div class="menu">
        <button class="menu-btn" @click="addTextbox">Add Tbox</button>
        <button class="menu-btn" @click="connectedSelected" :disabled="cannotConnect">Connect</button>
    </div>
    <drag-text
        v-for="t,i in textboxes"
        :key="t.id"
        :updateLinesHandler="updateLines"
        :updateTextbox="updateTextbox(i)"
        :deleteTextbox="deleteTextbox(i)"
        :selectTextbox="selectTextbox(i)"
        :updateTextboxText="updateTextboxText(i)"
        :selected="t.selected"
        :text="t.text"
        :startX="t.x" 
        :startY="t.y"/>
</template>

<script>
import DragText from './DragText.vue';
const { v4: uuid } = require("uuid")

export default {
    components:{DragText},
    props:{
        curTextboxes: Array,
        curLines: Array,
        handleClose: Function,
        updateTextboxesHandler: Function, 
        updateLinesHandler: Function,
    }, 
    // TODO: maybe move this canvas resizing into a specific canvas component since it is not 
    // not really app logic...
    mounted(){
        this.clearCanvasAndDrawLines();
    },
    computed:{
        cannotConnect(){
            return this.textboxes.filter(t => t.selected).length !== 2;
        }
    },
    data(){
        return{
            // canvas
            width: window.innerWidth,
            height: window.innerHeight,
            // app 
            textboxes: this.curTextboxes,
            lines: this.curLines,
        }
    },
    methods: {
        addTextbox(){
            this.textboxes.push(
                {
                    x: 500, 
                    y: 500,
                    text: 'click edit to change text',
                    selected: false,
                    id: uuid()
                }
            )
        },
        selectTextbox(idx){
            return () =>{
                this.textboxes[idx].selected = !this.textboxes[idx].selected;
            }
        },
        deleteTextbox(idx){
            return () =>{
                this.textboxes = this.textboxes.filter( (_, i) => i !== idx);
            }
        },
        updateTextbox(idx){
            return (x, y) =>{
                this.textboxes[idx] = { ...this.textboxes[idx],x, y };
            }
        },
        updateTextboxText(idx){
            return (text) =>{
                this.textboxes[idx] = {...this.textboxes[idx], text};
            }
        },
        clearCanvasAndDrawLines(){
            const ctx = this.$refs.canvas.getContext("2d");
            ctx.clearRect(0, 0, this.width, this.height);

            this.lines.forEach( line =>{
                // console.log(`drawing line from (${line.fromX},${line.fromY}) to (${line.toX},${line.toY})` );
                ctx.beginPath();
                ctx.moveTo(line.fromX, line.fromY);
                ctx.lineTo(line.toX, line.toY);
                ctx.stroke();
            })
        },
        updateLines(oldX, oldY, newX, newY){
            const deletingLine = (newX === null || newX === undefined) 
            && (newY === null || newY === undefined);
            
            for(let i = 0; i < this.lines.length; i++){
                const {fromX, fromY, toX, toY} = this.lines[i];

                if(fromX === oldX && fromY === oldY){
                    if(deletingLine){
                        this.lines[i] = null; // placeholder to later delete
                    }else{
                        this.lines[i] = {fromX: newX, fromY: newY, toX, toY};
                    }

                }else if (toX === oldX && toY === oldY){
                    if(deletingLine){
                        this.lines[i] = null; // placeholder to delete line
                    }else{
                        this.lines[i] = {fromX, fromY, toX: newX, toY: newY};
                    }
                }
            }
            this.lines = this.lines.filter( line => line !== null); // filter deleted lines
            this.clearCanvasAndDrawLines();
        },
        connectedSelected(){
            const [b1, b2] = this.textboxes.filter( t => t.selected);
            const newLine = { fromX: b1.x, fromY: b1.y, toX: b2.x, toY: b2.y};
            this.lines.push(newLine);
            this.clearCanvasAndDrawLines();
            // deselect all post connection
            this.textboxes.forEach( t => t.selected = false);
        }
    },
    watch:{
        textboxes: {
            handler(newTextboxes){
                this.updateTextboxesHandler(newTextboxes)
            },
            deep: true // 
        },
        lines(newLines){
            this.updateLinesHandler(newLines);
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
    background-color: #DCDCDC;
}
.menu{
    position: absolute;
    display: flex;
    align-items: center;
}
.menu-btn{
    padding: 10px 15px;
}
.close{
    position: absolute;
    right: 50px;
    top: 0;
}
</style>