<template>
  <menu-bar></menu-bar>
</template>

<script>

import MenuBar from './components/MenuBar.vue';

export default {
  name: "App",

  components: {
    MenuBar
    
  },


  
  methods: {
    
    // menuRoute(index) {
    //   if (index == 0) {
    //     this.$router.push("/")
    //   }
    //   if(index == 1){
    //     this.$router.push("/categoria");  
    //   }
      
    // }
  }
}
</script>
