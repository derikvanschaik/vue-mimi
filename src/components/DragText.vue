<template>
  <div
    ref="textbox"
    class="textbox"
    @mousemove="handleDrag"
    @mousedown="handleDragStart"
    @mouseup="handleDragEnd">
    {{ text }}
  </div>
</template>

<script>

export default {
  name: 'App',
  props:{
    updateLinesHandler: Function,
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
      text: 'hello world',
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
      }
    },
    handleDragEnd(){
      this.isDragging = false;
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
