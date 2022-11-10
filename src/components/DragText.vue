<template>
  <div
    ref="textbox"
    class="textbox"
    @mousemove="handleDrag"
    @mousedown="handleDragStart"
    @mouseup="handleDragEnd">
    {{ text }}
    <button @click="handleDelete">Delete</button>
    <input @click="handleSelect" type="checkbox" />
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
<style>
.textbox{
  background-color: grey;
  position: absolute;
  padding: 50px 50px;
  cursor: pointer;
  user-select: none;
  color: white;
  font-size: x-large;
}
</style>
