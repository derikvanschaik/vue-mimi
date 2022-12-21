<template>
    <mind-map
      :key="idx"
      v-if="idx !== null"
      :curTextboxes="mindmaps[idx].textboxes" 
      :curLines="mindmaps[idx].lines"
      :handleClose="()=> setIdx(null)"
      :updateTextboxesHandler="updateTextboxes"
      :updateLinesHandler="updateLines"
      :navigateMindmap="navigateMindmapCallback()"
      :path="path"
      :pathChange="pathChangeCallback()"
      :mindmaps="mindmaps"
      :createLinkHandler="createLink"/>
    <div v-else class="container">
      <div class="row">
        <h1 class="my-5">Mindmaps</h1>
      </div>
      <div class="row">
        <div class="col-8">
          <ul class="list-group">
            <li v-for="mindmap, i in mindmaps" :key="i" class="list-group-item">
              <a href="#" @click="setIdx(i)">{{ mindmap.title}}</a>
            </li>
          </ul>
        </div>
        <div class="col-4">
          <button
            @click="openCreateNewModal"
            class="btn btn-secondary">New Mindmap</button>
        </div>
      </div>
      <!-- modals -->
      <modal-component :handleClose="closeModal" :show="showCreateNewModal">
        <div class="form-group my-5">
          <label for="mindmap-name">Mindmap Name</label>
          <input 
            class="form-control" 
            name="mindmap-name" 
            placeholder="Enter new mindmap name" 
            v-model="newMindmapName" >
        </div>
        <button class="btn btn-secondary my-3" @click="createNewMindmap">Create</button>
        <button class="btn btn-outline-danger" @click="closeModal">Cancel</button>
      </modal-component>
    </div>


</template>

<script>
import MindMap from './components/MindMap.vue';
import ModalComponent from './components/ModalComponent.vue'
const { v4: uuid } = require("uuid")

export default {
  components:{
    MindMap,
    ModalComponent
  },
  computed : {
    showCreateNewModal(){
      return this.modal.open === true && this.modal.type === 'create'
    }
  },
  data(){
    return{
        modal: { open: false, type: 'create'}, // type = create || delete
        newMindmapName : '',
        idx : null,
        mindmaps:[
          {
            title: 'My Company',
            textboxes: [
                  { 
                    x: 600, 
                    y: 100, 
                    text: 'Careers', 
                    selected: false, 
                    id: uuid(),
                    link: 1, 
                 },
                 { 
                    x: 10, 
                    y: 100, 
                    text: 'Employees', 
                    selected: false, 
                    id: uuid(),
                    link: 2, 
                 },
              ],
              lines: [],
          },
          {
            title: 'Occupations',
            textboxes: [
                  { x: 100, y: 100, text: 'Engineering', selected: false, id: uuid(), link : 3 },
                  { x: 400, y: 500, text: 'Finance', selected: false, id: uuid(), link : 4 },
                  { x: 600, y: 100, text: 'Marketing', selected: false, id: uuid(), link : 5 },
              ],
              lines: []
          },
          {
            title: 'Employees @ company',
            textboxes: [
                  { x: 200, y: 500, text: 'Jeff Caronzo', selected: false, id: uuid(), link : -1 },
                  { x: 500, y: 500, text: 'Silas Bonana', selected: false, id: uuid(), link : -1 },
              ],
              lines: []
          },
          {
            title: 'types of Engineering',
            textboxes: [
                  { x: 100, y: 100, text: 'Software', selected: false, id: uuid(), link : -1 },
                  { x: 400, y: 500, text: 'Financial', selected: false, id: uuid(), link : -1 },
                  { x: 600, y: 100, text: 'Civil', selected: false, id: uuid(), link : -1 },
              ],
              lines: []
          },
          {
            title: 'types of Finance',
            textboxes: [
                  { x: 100, y: 100, text: 'Accounting', selected: false, id: uuid(), link : -1 },
                  { x: 400, y: 500, text: 'Legal', selected: false, id: uuid(), link : -1 },
                  { x: 600, y: 100, text: 'Venture Capital', selected: false, id: uuid(), link : -1 },
              ],
              lines: []
          },
          {
            title: 'types of Marketing',
            textboxes: [
                  { x: 100, y: 100, text: 'Digital', selected: false, id: uuid(), link : -1 },
                  { x: 400, y: 500, text: 'Media', selected: false, id: uuid(), link : -1 },
              ],
              lines: []
          },
        ],
        path: [], // [{link: 0, title: mindmap title}]

    }
  },
  watch: {
    idx(cur, old){
      if(cur !== null && old === null){
        this.path = [];
        this.addToPath(cur);
      }
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
        this.addToPath(idx);
      }
    },
    addToPath(idx){
      this.path.push({ link: idx, title: this.mindmaps[idx].title });
    },
    pathChangeCallback(){
      return (pathIdx, linkIdx) => {
        this.path = this.path.slice(0, pathIdx + 1);
        this.idx = linkIdx;
      }
    },
    createLink(textboxIdx, mindmapIdx){
      this.mindmaps[this.idx].textboxes[textboxIdx].link = mindmapIdx;
    },
    openCreateNewModal(){
      this.modal = { open: true, type: 'create'}
    },
    closeModal(){
      this.modal.open = false
    },
    createNewMindmap(){
      if(this.newMindmapName === ''){
        alert('Mindmap name cannot be blank!')
        return
      }
      const newMindmap = {title: this.newMindmapName, textboxes: [], lines : []}
      this.mindmaps.push(newMindmap)
      this.newMindmapName = ''
      this.modal.open = false
    }
  }
}
</script>

<style scoped>

ul{
  width: 100%
}
li {
  padding: 15px 20px;
  font-size: x-large;
}


</style>