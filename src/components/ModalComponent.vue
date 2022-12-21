<template>
    <!-- <div class="modal" v-show="show">
        <slot></slot>
    </div> -->

    <!-- The Modal -->
    <div class="modal" :style="{ 'display': show? 'block' : 'none'}">
        <!-- Modal content -->
        <div class="modal-content">
        <span class="close-btn" @click="handleClose()">&times;</span>
        <slot></slot>
        </div>
    </div>

</template>
<script>
export default {
    props: {
        show: Boolean,
        handleClose: Function,
    },
}
</script>
<style>

/* The Modal (background) */
.modal {
  display: none; /* Hidden by default */
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  padding-top: 100px; /* Location of the box */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
}

/* Modal Content */
.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 50%;
}

/* The Close Button */
/* Mindmap component has styling related to .close which overrides .close in here
so we have to make sure the classes don't get overriden by choosing different name */
.close-btn {
  color: #aaaaaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close-btn:hover,
.close-btn:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}
</style>