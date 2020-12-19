<template>
  <v-app id="inspire">
    <v-system-bar app>
      <v-spacer></v-spacer>

      <v-icon>mdi-square</v-icon>

      <v-icon>mdi-circle</v-icon>

      <v-icon>mdi-triangle</v-icon>
    </v-system-bar>

    <v-app-bar app>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title>Application</v-toolbar-title>
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" class="" fixed dark temporary>
      <v-card class="mx-auto deep-purple accent-4" height="100%" width="256">
        <!-- <v-navigation-drawer v-model="drawer" class="deep-purple accent-4" dark temporary>  -->
          <v-list>
            <!-- v-for="(item, index) in items" :key="index" -->
            <v-list-item v-for="(item, index) in items" :key="index" link :to="{name: item.ruta}" exact>
              
              <v-list-item-icon>
                <v-icon>{{ item.icon }}</v-icon>
              </v-list-item-icon>
            
              <v-list-item-content>
                <v-list-item-title >{{ item.title}} {{index}} </v-list-item-title>
              </v-list-item-content>
            </v-list-item>
            
          </v-list>
          
            <div class="pa-2">
              <v-btn block> Logout </v-btn>
            </div>
          
          
        <!-- </v-navigation-drawer> -->
      </v-card>
    </v-navigation-drawer>

    <v-main class="grey lighten-2">
      <v-container> 
        <router-view></router-view>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    HelloWorld,
  },

  data: () => ({
    drawer: null,
    items: [
      { title: "Inicio", icon: "mdi-home", ruta: 'Home' },
      { title: "Categorias", icon: "mdi-notification-clear-all", ruta:'Categoria' },
      { title: "Articulos", icon: "mdi-shopping", ruta: 'Articulo'},
      { title: "Usuarios", icon: "mdi-account-box", ruta: 'Usuario'},
    ],
    links: ["Categorias", "Articulos", "Usuarios"],
    mini: true,
  }),
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
