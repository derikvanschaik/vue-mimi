<template>
    <mind-map :curTextboxes="textboxes" :curLines="lines" />
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
        textboxes: [
                { x: 10, y: 100, text: 'hello', selected: false, id: uuid() },
                { x: 100, y: 500, text: 'world', selected: false, id: uuid() },
                { x: 300, y: 600, text: 'how are you?', selected: false, id: uuid() }
            ],
            lines: [ 
                { fromX: 10, fromY: 100, toX: 100, toY: 500},
                { fromX: 10, fromY: 100, toX: 300, toY: 600}
            ],
    }
  }
}
</script>