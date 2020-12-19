<template>
    <v-data-table
    :headers="headers"
    :items="categorias"
    sort-by="nombre"
    class="elevation-1"
    :loading="cargando"
    loading-text="Cargando... Por favor espere"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Categoria</v-toolbar-title>
        
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              Agregar Categoria
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.nombre"
                      label="Nombre"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="8"
                  >
                    <v-textarea
                      v-model="editedItem.descripcion"
                      label="Descripcion"
                      counter="254"
                      no-resize
                      auto-grow
                    ></v-textarea>
                  </v-col>
                  
                 
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancelar
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Guardar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="600px">
          <v-card>
            <v-card-title class="headline">Está seguro que desea deshabilitar esta categoría?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancelar</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">Aceptar</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
      <!-- <pre>{{$data.categorias}}</pre> -->
    </template>
    
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
    
  </v-data-table>
  
</template>

<script>
import axios from 'axios';
export default {
    data: () => ({
      titulo: ["Articulos","Categorias","Usuarios"],
      dialog: false,
      dialogDelete: false,
      cargando: true,
      headers: [
        {
          text: 'Nombre',
          align: 'start',
          sortable: true,
          value: 'nombre',
        },
        { text: 'Descripcion', value: 'descripcion' },
        { text: 'Estado', value: 'estado' },
        
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      desserts: [],
      categorias:[],
      editedIndex: -1,
      editedItem: {
        nombre: '',
        descripcion:''
      },
      defaultItem: {
        nombre: '',
        descripcion: '',
        
      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Agregar Categoria' : 'Editar Categoria'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
    },

    created () {
      this.initialize();
      this.list();
    },

    methods: {
      initialize () {
        this.desserts = [
          {
            name: 'Frozen Yogurt',
            calories: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
          }
        ]
      },
      list(){
        axios.get('http://localhost:3000/api/categoria/list')
        .then(
          response => {
            this.categorias = response.data;
            this.cargando = false;
          }
        )
        .catch(error => {return error})
      },

      editItem (item) {
        this.editedIndex = item.id;//this.categorias.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        this.editedIndex = item.id;//this.categorias.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        this.categorias.splice(this.editedIndex, 1)
        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      save () {
        if (this.editedIndex > -1) {

          //editar objeto
          axios.put('http://localhost:3000/api/categoria/update', {
            id: this.editedItem.id,
            nombre: this.editedItem.nombre,
            descripcion: this.editedItem.descripcion 
            })
          .then(response=>{
            console.log(response);
            this.list();
          })
          .catch(error =>{
            console.log(error)
            return error;
          })
          // Object.assign(this.desserts[this.editedIndex], this.editedItem)
        } else {
          //agregar un objeto
          axios.post('http://localhost:3000/api/categoria/add',{
            nombre:this.editedItem.nombre,
            descripcion:this.editedItem.descripcion,
            estado: 1
            })
          .then(response=>{
            console.log(response);
            this.list();
          })
          .catch(error =>{
            console.log(error)
            return error;
          })
          //this.desserts.push(this.editedItem)
        }
        this.close()
      },
    },
}
</script>

<style scoped>

</style>