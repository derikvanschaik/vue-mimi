<template>
    <canvas :width="width" :height="height" ref="canvas"></canvas>
    <div @click="handleClose" class="close">
        <span class="close-icon">+</span>
    </div>
    <div class="menu">
        <button class="menu-btn btn btn-light btn-outline-dark mx-1" @click="addTextbox">Add Tbox</button>
        <button class="menu-btn btn btn-light btn-outline-dark mx-1" @click="connectedSelected" :disabled="cannotConnect">Connect</button>
        <button class="menu-btn btn btn-light btn-outline-dark mx-1" @click="toggleShowMindmapList" :disabled="cannotLink">Link</button>
    </div>
    <drag-text
        v-for="t,i in textboxes"
        :key="t.id"
        :idx="i"
        @drag-start="handleDragStart"
        :updateLinesHandler="updateLines"
        :updateTextbox="updateTextbox(i)"
        :deleteTextbox="deleteTextbox(i)"
        :selectTextbox="selectTextbox(i)"
        :updateTextboxText="updateTextboxText(i)"
        :selected="t.selected"
        :text="t.text"
        :startX="t.x" 
        :startY="t.y"
        :link="t.link"
        :navigateMindmap="navigateMindmap"/>

        <ul class="path">
            <link-component
                class="link"
                v-for="p,i in path" 
                :key="p" 
                :text="p.title"
                :handleNavigate="() => handlePathChange(i, p.link )"/>
        </ul>

        <!-- TODO: create a component for this... -->
        <modal-component :show="showMindmapList" :handleClose="() => showMindmapList = false">
            <h1>Create a link to one of the mindmaps below</h1>
            <select
                v-model="linkedMindmapIdx" >
                <option 
                    v-for="mindmap,idx in mindmaps"
                    :key="idx"
                    :value="idx">
                    {{ mindmap.title }}
                </option>
            </select>
            <div>
                <button @click="linkTextboxToMindmap">Create Link</button>
                <button @click="toggleShowMindmapList">Cancel</button>
            </div>
        </modal-component>

</template>

<script>
import DragText from './DragText.vue';
import LinkComponent from './LinkComponent.vue';
import ModalComponent from './ModalComponent.vue';
const { v4: uuid } = require("uuid")

export default {
    components:{DragText, LinkComponent, ModalComponent},
    props:{
        curTextboxes: Array,
        curLines: Array,
        handleClose: Function,
        updateTextboxesHandler: Function, 
        updateLinesHandler: Function,
        navigateMindmap: Function,
        path: Array,
        pathChange: Function,
        mindmaps: Array,
        createLinkHandler: Function,
    }, 
    // TODO: maybe move this canvas resizing into a specific canvas component since it is not 
    // not really app logic...
    mounted(){
        this.clearCanvasAndDrawLines();
        // todo: remove these functions on onmounted
        document.addEventListener("mousemove", this.handleMouseMove);
        document.addEventListener("mouseup", () => this.isDragging = false);
    },
    computed:{
        cannotConnect(){
            return this.textboxes.filter(t => t.selected).length !== 2;
        },
        cannotLink(){
            return this.textboxes.filter(t => t.selected).length !== 1;
        },
    },
    data(){
        return{
            // canvas
            width: window.innerWidth,
            height: window.innerHeight,
            // app 
            textboxes: this.curTextboxes,
            lines: this.curLines,
            isDragging: false,
            x: 0,
            y: 0,
            dragBoxIdx: -1,
            showMindmapList: false,
            linkedMindmapIdx : null,
        }
    },
    methods: {
        handleDragStart(x, y, i){
            this.isDragging = true;
            this.x = x;
            this.y = y;
            this.dragBoxIdx = i;
        },
        handleMouseMove(e){
            if(this.isDragging){
                const dx = e.clientX - this.x;
                const dy = e.clientY - this.y;
                
                const oldX = this.textboxes[this.dragBoxIdx].x;
                const oldY = this.textboxes[this.dragBoxIdx].y;

                this.textboxes[this.dragBoxIdx].x += dx;
                this.textboxes[this.dragBoxIdx].y += dy;


                this.x = e.clientX;
                this.y = e.clientY;

                // reupdate array to force update on element positions...
                this.textboxes = this.textboxes.map(t => t);
                // update lines
                this.updateLines(oldX, oldY, 
                    this.textboxes[this.dragBoxIdx].x, this.textboxes[this.dragBoxIdx].y);
            }
        },
        addTextbox(){
            this.textboxes.push(
                {
                    x: 500, 
                    y: 500,
                    text: 'click edit to change text',
                    selected: false,
                    id: uuid(),
                    link: -1
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
        },
        handlePathChange(idx, link){
            this.pathChange(idx, link);
        },
        toggleShowMindmapList(){
            this.showMindmapList = !this.showMindmapList;
            if(this.showMindmapList){
                this.linkedMindmapIdx = this.textboxes.find( t => t.selected === true).link;
                if(this.linkedMindmapIdx === -1){
                    this.linkedMindmapIdx = null;
                }
            }else{
                this.linkedMindmapIdx = null;
            }
        },
        linkTextboxToMindmap(){
            const tboxIdx = this.textboxes.indexOf( this.textboxes.find( t => t.selected === true));
            this.createLinkHandler(tboxIdx, this.linkedMindmapIdx);
            this.toggleShowMindmapList(); // set to false
            this.textboxes.forEach( t => t.selected = false);
        },
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
    cursor: pointer;
}
.close-icon {
  font-size: 60px;
  font-weight: 600;
  display: inline-block;
  transform: rotate(45deg);
}
.path{
    background-color: #f2f2f2;
}
.path{
    overflow: hidden;
    position: fixed;
    bottom: 0;
    width: 100%;
}
.link {
  float: left;
  display: block;
  text-align: center;
  padding: 14px 16px;
  font-size: 17px;
}
.selected{
    background-color: blue;
}

</style>