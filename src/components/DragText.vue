<template>
  <div
    ref="textbox"
    class="textbox"
    @mousedown="emitDragStart">
    <div v-if="!isEditing" class="textbox-text">
      <div v-if="link >= 0">
        <link-component
          :handleNavigate="handleNavigate"
          :text="inputText" />
      </div>
      <div v-else>
        {{ inputText }}
      </div>
    </div>
    <textarea v-else v-model="inputText" rows="7" @input="handleInput" class=".text-area"></textarea>

    <span class="delete" @click="handleDelete">&times;</span>
    <button @click="toggleIsEditing">{{ isEditing? 'Done': 'Edit'}}</button>
    <input @click="handleSelect" type="checkbox" class="select" :checked="selected"/>

  </div>
</template>

<script>
import LinkComponent from './LinkComponent.vue';

export default {
  name: 'App',
  components: { LinkComponent },
  props:{
    updateLinesHandler: Function,
    updateTextbox: Function,
    deleteTextbox: Function,
    selectTextbox: Function,
    updateTextboxText: Function,
    selected: Boolean,
    text: String,
    startX: Number,
    startY: Number,
    idx: Number,
    link: Number,
    navigateMindmap: Function,
  }, 
  mounted(){
    this.repositionTextBox();
  }, 
  updated(){
    this.repositionTextBox();
  }, 
  data(){
    return{
      x: this.startX,
      y: this.startY,
      isEditing: false,
      inputText: this.text,

    }
  },
  methods: {
    repositionTextBox(){
      // Set the position of element
      this.$refs.textbox.style.left = `${this.startX}px`;
      this.$refs.textbox.style.top = `${this.startY}px`;
    },
    emitDragStart(e){
      if(this.isEditing) return;
      // set the current mouse position
      this.$emit('dragStart',e.clientX, e.clientY, this.idx);
    },
    handleDelete(){
      this.deleteTextbox();
      this.updateLinesHandler(this.$refs.textbox.offsetLeft, this.$refs.textbox.offsetTop);
    },
    handleSelect(e){
      e.stopPropagation();
      // console.log(this.$refs.textbox.offsetLeft, this.$refs.textbox.offsetTop);
      this.selectTextbox();
    },
    toggleIsEditing(){
      this.isEditing = !this.isEditing;
      if(!this.isEditing){
        this.updateTextboxText(this.inputText);
      }
    },
    handleNavigate(){
      this.navigateMindmap(this.link);
    }
  }
}
</script>
<style scoped>
.textbox{
  background-color: white;
  color: black;
  position: absolute;
  padding: 15px 5px;
  cursor: pointer;
  user-select: none;
  box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
  border-radius: 4px;
  border: 1px solid #42b883;
  max-width: 250px;
  word-wrap: break-word;
}

.delete, .select{
  position: absolute;
  top: 0px;
}
.delete{
  left: 0;
  font-size: xx-large;
  top: -20px;
}
.select{
  right: 0;
}
.textbox-text{
  margin-bottom: 10px;
}
.text-area {
  height: 100%;
  width: 100%
}
</style>
