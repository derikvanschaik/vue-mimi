<template>
    <mind-map
      :key="idx"
      v-if="idx !== null"
      :curTextboxes="mindmaps[idx].textboxes" 
      :curLines="mindmaps[idx].lines"
      :handleClose="()=> setIdx(null)"
      :updateTextboxesHandler="updateTextboxes"
      :updateLinesHandler="updateLines"
      :navigateMindmap="navigateMindmapCallback()"/>
    <div v-else class="container">
      <ul>
        <li v-for="mindmap, i in mindmaps" :key="i" @click="setIdx(i)">
          <a href="#">{{ mindmap.title}}</a>
        </li>
      </ul>
    </div>


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
                  { 
                    x: 10, 
                    y: 100, 
                    text: 'link to 2nd mindmap', 
                    selected: false, 
                    id: uuid(),
                    link: 1, 
                 },
              ],
              lines: [],
          },
          {
            title: 'my second mindmap',
            textboxes: [
                  { x: 100, y: 100, text: 'link to third mindmap', selected: false, id: uuid(), link : 2 },
                  { x: 400, y: 500, text: 'random text', selected: false, id: uuid(), link : -1 },
                  { x: 600, y: 100, text: 'more random text', selected: false, id: uuid(), link : -1 },
              ],
              lines: [ 
                      { fromX: 100, fromY: 100, toX: 400, toY: 500},
                      { fromX: 100, fromY: 100, toX: 600, toY: 100},
                    ]
          },
          {
            title: 'my third mindmap',
            textboxes: [
                  { x: 200, y: 500, text: 'link to first mindmap', selected: false, id: uuid(), link : 0 },
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
    },
    navigateMindmapCallback(){
      return (idx) =>{
        this.setIdx(idx);
      }
    }
  }
}
</script>

<style scoped>
.container{
  background-color: #34495E;
  height: 100vh;
  display: flex;
}
ul {
  list-style: none;
  margin: auto;
  height: 80%;
  width: 50%;
  background-color: #41B883;
  padding: 50px 50px;
  text-align: center;
}
li{
  background-color: white;
  box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2);
  padding: 15px 20px;
  margin: 25px 25px;
}
a{
  color: #42b883;
  font-size: x-large;
}

</style>