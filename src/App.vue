<template>
    <mind-map
      v-if="idx !== null"
      :curTextboxes="mindmaps[idx].textboxes" 
      :curLines="mindmaps[idx].lines"
      :handleClose="()=> setIdx(null)"
      :updateTextboxesHandler="updateTextboxes"
      :updateLinesHandler="updateLines"/>
    <ul v-else>
      <li v-for="mindmap, i in mindmaps" :key="i" @click="setIdx(i)">
        <a href="#">{{ mindmap.title}}</a>
      </li>
    </ul>
</template>

<script>
import MindMap from './components/MindMap.vue';
const { v4: uuid } = require("uuid")

export default {
  components:{
    MindMap
  },
  data(){
    return{
        idx : null,
        mindmaps:[
          {
            title: 'mindmap1',
            textboxes: [
                  { x: 10, y: 100, text: 'hello', selected: false, id: uuid() },
                  { x: 100, y: 500, text: 'world', selected: false, id: uuid() },
                  { x: 300, y: 600, text: 'how are you?', selected: false, id: uuid() }
              ],
              lines: [ 
                  { fromX: 10, fromY: 100, toX: 100, toY: 500},
                  { fromX: 10, fromY: 100, toX: 300, toY: 600}
              ],
          },
          {
            title: 'my second mindmap',
            textboxes: [
                  { x: 100, y: 100, text: 'whatsup dude!??', selected: false, id: uuid() },
              ],
              lines: []
          }
        ]

    }
  },
  methods:{
    setIdx(i){
      this.idx = i;
    },
    updateTextboxes(textboxes){
      this.mindmaps[this.idx].textboxes = textboxes;
    },
    updateLines(lines){
      this.mindmaps[this.idx].lines = lines;
    }
  }
}
</script>