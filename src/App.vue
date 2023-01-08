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
    <div v-else class="container my-2">
      <div class="row">
        <div class="jumbotron my-1 rounded">
          <h1 class="display-4">MIMI: Mindmaps made easy</h1>
          <p class="lead">Either click one of the links below to explore existing mindmaps, or create a 
            new mindmap on the right!
          </p>
        </div>
        
      </div>
      <div class="row">
        <div class="col-8">
          <ul class="list-group">
            <li v-for="mindmap, i in mindmaps" :key="i" class="list-group-item d-flex justify-content-between">
              <a href="#" @click="setIdx(i)">{{ mindmap.title}}</a>
              <div class="action-menu">
                  <button 
                    type="button" 
                    class="btn btn-outline-dark mx-3"
                    @click="openEditMindmapModal(i)">Edit</button>
                  <button 
                    type="button" 
                    class="btn btn-outline-danger"
                    @click="openDeleteMindmapModal(i)">Delete</button>
              </div>
            </li>
          </ul>
        </div>
        <div class="col-4">
          <button
            @click="openCreateNewModal"
            class="btn btn-secondary">New Mindmap</button>
        </div>
      </div>
      <!-- create new mindmap -->
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

      <!-- edit existing mindmap name -->
      <modal-component :handleClose="closeModal" :show="showEditModal">
        <div class="form-group my-5">
          <label for="mindmap-name">Enter new name for mindmap '{{ previousTitle }}' :</label>
          <input 
            class="form-control" 
            name="mindmap-name" 
            placeholder="Enter new mindmap name"
            v-model="newMindmapName" >
        </div>
        <button class="btn btn-secondary my-3" @click="editMindmapTitle">Change</button>
        <button class="btn btn-outline-danger" @click="closeModal">Cancel</button>
      </modal-component>
      
      <!-- delete mindmap modal -->
      <modal-component :handleClose="closeModal" :show="showDeleteModal">
        <h4>This action cannot be undone. Please confirm.</h4>
        <div class="alert alert-danger" v-if="getLinkReferenceCount > 0">
          <h4>WARNING</h4>
          <p>
            you have {{ getLinkReferenceCount }} link{{ getLinkReferenceCount > 1?'s': ''}} to this mindmpap,
            deleting this mindmap will make them dead links.
          </p>
        </div>
        <button class="btn btn-danger my-3" @click="deleteMindmap">Delete</button>
        <button class="btn btn-secondary" @click="closeModal">Cancel</button>
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
    previousTitle(){
      if (this.editIdx !== null){
        return this.mindmaps[this.editIdx].title
      }
      return ''
    },
    showCreateNewModal(){
      return this.modal.open === true && this.modal.type === 'create'
    },
    showEditModal(){
      return this.modal.open === true && this.modal.type === 'edit'
    },
    showDeleteModal(){
      return this.modal.open === true && this.modal.type === 'delete';
    },
    getLinkReferenceCount(){
      let count = 0;
      if(this.deleteIdx !== null){
        for(const m of this.mindmaps){
          for(const t of m.textboxes){
            if (t.link === this.deleteIdx){
              count++;
            }
          }
        }
      }
      return count;
    }
  },
  data(){
    return{
        modal: { open: false, type: 'create'}, // type = create || edit || delete
        newMindmapName : '',
        deleteIdx: null,
        editIdx: null,
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
    setEditIdx(i){
      this.editIdx = i;
    },
    setDeleteIdx(i){
      this.deleteIdx = i;
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
    openEditMindmapModal(i){
      this.setEditIdx(i)
      this.modal = {open: true, type: 'edit'}
    },
    openDeleteMindmapModal(i){
      this.setDeleteIdx(i)
      this.modal = {open: true, type: 'delete'}
    },
    closeModal(){
      this.modal.open = false
    },
    isMindmapNameEmpty(){
      if(this.newMindmapName === ''){
        alert('Mindmap name cannot be blank!')
        return true
      }
      return false
    }, 
    createNewMindmap(){
      if (this.isMindmapNameEmpty() ){
        return
      }
      const newMindmap = {title: this.newMindmapName, textboxes: [], lines : []}
      this.mindmaps.push(newMindmap)
      this.newMindmapName = ''
      this.modal.open = false
    },
    editMindmapTitle(){
      if (this.isMindmapNameEmpty() ){
        return
      }
      this.mindmaps[this.editIdx].title = this.newMindmapName
      this.newMindmapName = ''
      this.modal.open = false
    },
    deleteMindmap(){
      // before deleting we need to remove all dead links to this mindmap....
      // if not this will inaccurately index links into the mindmpas array
      // the fix requires we create unique ids for each mindmap so that links reference the id
      // instead of their idx
      // Note: this may affect the path component as well, need to look into that. 
      // for now we just provide an error message to the user notifying them of strange behaviours that
      // may occur from this action.

      // current fix for this issue -> decrement all the link values of every textbox that links to a mindmap 
      // that comes after the deleted mindmap and set the links to -1 if they are referencing the current mindmap

      // update all current links that point to or after the deleted mindmap idx
      for(const m of this.mindmaps){
        for (const t of m.textboxes){
          if (t.link === this.deleteIdx){
            t.link = -1;
          }
          if (t.link > this.deleteIdx){
            t.link -= 1
          }
        }
      }
      // delete mindmap
      this.mindmaps = [
        ...this.mindmaps.slice(0, this.deleteIdx),
         ...this.mindmaps.slice(this.deleteIdx + 1, this.mindmaps.length)]
      this.modal.open = false
    },

  },

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
a{
  color: slategrey;
}
.jumbotron{
  background-color: lightslategrey;
  color: whitesmoke;
  padding: 5em 4em;
}
.lead{
  color: white
}

</style>