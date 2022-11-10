<template>
  <div
    ref="textbox"
    class="textbox"
    @mousemove="handleDrag"
    @mousedown="handleDragStart"
    @mouseup="handleDragEnd">
    {{ text }}
    <button @click="handleDelete" class="delete">Delete</button>
    <input @click="handleSelect" type="checkbox" class="select" :checked="selected"/>

  </div>
</template>

<script>

export default {
  name: 'App',
  props:{
    updateLinesHandler: Function,
    updateTextbox: Function,
    deleteTextbox: Function,
    selectTextbox: Function,
    selected: Boolean,
    text: String,
    startX: Number,
    startY: Number
  }, 
  mounted(){
    // Set the position of element
    this.$refs.textbox.style.left = `${this.startX}px`;
    this.$refs.textbox.style.top = `${this.startY}px`;
  }, 
  data(){
    return{
      x: this.startX,
      y: this.startY,
      isDragging: false
    }
  },
  methods: {

    handleDragStart(e){
      // set the current mouse position
      this.x = e.clientX;
      this.y = e.clientY;
      this.isDragging = true;
    },
    handleDrag(e){
      if(this.isDragging){

        if(e.target !== this.$refs.textbox) return
        // draw textbox 
        const dx = e.clientX - this.x;
        const dy = e.clientY - this.y;

        // get old pos 
        const oldOffsetTop = e.target.offsetTop;
        const oldOffsetLeft = e.target.offsetLeft;
        // Set the position of element
        e.target.style.top = `${e.target.offsetTop + dy}px`;
        e.target.style.left = `${e.target.offsetLeft + dx}px`;

        // Reassign the position of mouse
        this.x = e.clientX;
        this.y = e.clientY;
        // call callback
        
        // values are left and top for x and y -- is this what we want though?...
        this.updateLinesHandler(
          oldOffsetLeft, oldOffsetTop,
          (e.target.offsetLeft),(e.target.offsetTop));
          
        this.updateTextbox(e.target.offsetLeft, e.target.offsetTop);
      }
    },
    handleDragEnd(){
      this.updateTextbox(this.$refs.textbox.offsetLeft, this.$refs.textbox.offsetTop);
      this.isDragging = false;
    },
    handleDelete(){
      this.deleteTextbox();
      this.updateLinesHandler(this.$refs.textbox.offsetLeft, this.$refs.textbox.offsetTop);
    },
    handleSelect(e){
      e.stopPropagation();
      // console.log(this.$refs.textbox.offsetLeft, this.$refs.textbox.offsetTop);
      this.selectTextbox();
    }
  }
}
</script>
<style scoped>
.textbox{
  background-color: white;
  color: black;
  position: absolute;
  padding: 50px 50px;
  cursor: pointer;
  user-select: none;
  box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
  border-radius: 4px;
  border: 1px solid #42b883;
}

.delete, .select{
  position: absolute;
  top: 0;
}
.delete{
  left: 0;
}
.select{
  right: 0;
}
</style>
