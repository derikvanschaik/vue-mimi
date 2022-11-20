<template>
    <a href="#" @click="handleNavigate">
        {{ text }}
    </a>
</template>
  
<script>
  export default {
    props:{
      text: String,
      handleNavigate: Function,
    }
  }
  </script>