<template>
    <canvas :width="width" :height="height" ref="canvas"></canvas>
    <div class="menu">
        <button class="menu-btn" @click="addTextbox">Add Tbox</button>
        <button class="menu-btn" @click="connectedSelected" :disabled="cannotConnect">Connect</button>
    </div>
    <drag-text
        v-for="t,i in textboxes"
        :key="t.text"
        :updateLinesHandler="updateLines"
        :updateTextbox="updateTextbox(i)"
        :deleteTextbox="deleteTextbox(i)"
        :selectTextbox="selectTextbox(i)"
        :text="t.text"
        :startX="t.x" 
        :startY="t.y"/>
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
            textboxes: [
                { x: 10, y: 100, text: 'hello', selected: false },
                { x: 100, y: 500, text: 'world', selected: false },
                { x: 300, y: 600, text: 'how are you?', selected: false}
            ],
            lines: [ 
                { fromX: 10, fromY: 100, toX: 100, toY: 500},
                { fromX: 10, fromY: 100, toX: 300, toY: 600}
            ],

        }
    },
    methods: {
        addTextbox(){
            this.textboxes.push(
                {
                    x: 500, 
                    y: 500,
                    // TODO: this will cause a bug related to the keys of the drag box 
                    // component
                    // solution is to get a unique id for each textbox and not use textbox's text
                    // as a unique key...
                    text: 'textbox (' + this.textboxes.length + ')',
                    selected: false
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
        // so far only redraws -- may need to add and delete lines in the future as well
        // maybe we can add a final param that is a string describing the action 
        // like: updateLines(action="DELETE", oldX, oldY, newX, newY)
        // or we can update and check the param count..
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
            console.log("connecting:",b1, b2, newLine);
            this.lines.push(newLine);
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
</style>