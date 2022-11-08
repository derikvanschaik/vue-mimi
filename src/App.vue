<template>
  <div 
    :key="i"
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
  data(){
    return{
      text: 'hello world',
      x: 0,
      y: 0,
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

        const dx = e.clientX - this.x;
        const dy = e.clientY - this.y;

        // Set the position of element
        e.target.style.top = `${e.target.offsetTop + dy}px`;
        e.target.style.left = `${e.target.offsetLeft + dx}px`;

        // Reassign the position of mouse
        this.x = e.clientX;
        this.y = e.clientY;
      }
    },
    handleDragEnd(){
      this.isDragging = false;
    }
  }
}
</script>
<style>
* {
  margin: 0;
  padding: 0;
}
.textbox{
  background-color: grey;
  position: absolute;
  padding: 50px 50px;
  cursor: pointer;
}
</style>
