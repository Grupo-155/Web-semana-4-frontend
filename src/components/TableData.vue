<template>
  <v-data-table
    :headers="headers"
    :items="informacion"
    sort-by="nombre"
    class="elevation-1"
    :loading="cargando"
    loading-text="Cargando... Por favor espere"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>
          {{
            ruta
              .trim()
              .toLowerCase()
              .replace(/\w\S*/g, (w) =>
                w.replace(/^\w/, (c) => c.toUpperCase())
              )
          }}</v-toolbar-title
        >

        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on, attrs }">
            <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
              Agregar {{ ruta }}
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }} {{ ruta }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <!--columnas para categoria-->
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'categoria'">
                    <v-text-field
                      v-model="editedCategoryItem.nombre"
                      label="Nombre"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="8" v-if="ruta == 'categoria'">
                    <v-textarea
                      v-model="editedCategoryItem.descripcion"
                      label="Descripcion"
                      counter="254"
                      no-resize
                      auto-grow
                    ></v-textarea>
                  </v-col>
                  <!--Columnas para articulo-->
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'articulo'">
                    <v-text-field
                      v-model="editedArticleItem.nombre"
                      label="Nombre"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'articulo'">
                    <v-text-field
                      v-model="editedArticleItem.descripcion"
                      label="Descripcion"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'articulo'">
                    <v-text-field
                      v-model="editedArticleItem.codigo"
                      label="Codigo"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'articulo'">
                    <v-text-field
                      v-model="editedArticleItem.precio_venta"
                      label="Precio de venta"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'articulo'">
                    <v-text-field
                      v-model="editedArticleItem.stock"
                      label="Stock"
                    ></v-text-field>
                  </v-col>
                  <!--Columnas para usuario-->
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'usuario'">
                    <v-text-field
                      v-model="editedUserItem.nombre"
                      label="Nombre"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'usuario'">
                    <v-text-field
                      v-model="editedUserItem.rol"
                      label="Rol"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'usuario'">
                    <v-text-field
                      v-model="editedUserItem.email"
                      label="Email"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'usuario'">
                    <v-text-field
                      v-model="editedUserItem.tipo_documento"
                      label="Tipo de Documento"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'usuario'">
                    <v-text-field
                      v-model="editedUserItem.num_documento"
                      label="Numero de documento"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4" v-if="ruta == 'usuario'">
                    <v-text-field
                      v-model="editedUserItem.telefono"
                      label="Telefono"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">
                Cancelar
              </v-btn>
              <v-btn color="blue darken-1" text @click="save"> Guardar </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="600px">
          <v-card>
            <v-card-title class="headline"
              >Está seguro que desea deshabilitar {{ genero }}
              {{ ruta }}?</v-card-title
            >
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete"
                >Cancelar</v-btn
              >
              <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                >Aceptar</v-btn
              >
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
      <!-- <pre>{{$data.categorias}}</pre> -->
    </template>

    <template v-slot:item.actions="{ item }">
      <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
      <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize"> Reset </v-btn>
    </template>
  </v-data-table>
</template>

<script>
import axios from "axios";

export default {
  data: () => ({
    titulo: ["Articulos", "Categorias", "Usuarios"],
    dialog: false,
    dialogDelete: false,
    cargando: true,

    headers: [
      {
        text: "Nombre",
        align: "start",
        sortable: true,
        value: "nombre",
      },
      { text: "Descripcion", value: "descripcion" },

      { text: "Estado", value: "estado" },

      { text: "Actions", value: "actions", sortable: false },
    ],
    desserts: [],
    informacion: [],
    editedIndex: -1,
    editedItem: {
      nombre: "",
      descripcion: "",
    },
    defaultItem: {
      nombre: "",
      descripcion: "",
    },
    //prueba
    editedCategoryItem: {
      nombre: "",
      descripcion: "",
    },
    defaultCategoryItem: {
      nombre: "",
      descripcion: "",
    },
    editedArticleItem: {
      nombre: "",
      descripcion: "",
      codigo: "",
      precio_venta: null,
      stock: null,
    },
    defaultArticleItem: {
      nombre: "",
      descripcion: "",
      codigo: "",
      precio_venta: null,
      stock: null,
    },
    editedUserItem: {
      nombre: "",
      rol: "",
      email: "",
      tipo_documento: "",
      num_documento: "",
      direccion: "",
      telefono: "",
    },
    defaultUserItem: {
      nombre: "",
      rol: "",
      email: "",
      tipo_documento: "",
      num_documento: "",
      direccion: "",
      telefono: "",
    },
  }),
  props: ["ruta"],

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Agregar" : "Editar";
    },
    genero() {
      if (this.ruta === "usuario" || this.ruta == "articulo") {
        return "este";
      } else {
        return "esta";
      }
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.initialize();
    this.list(this.ruta);
  },

  methods: {
    initialize() {
      this.desserts = [
        {
          name: "Frozen Yogurt",
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
        },
      ];
    },
    list(endpoint) {
      axios
        .get(`http://localhost:3000/api/${endpoint}/list`)
        .then((response) => {
          this.informacion = response.data; //ruta obtiene el nombre categoria, ussuario o articulo
          this.cargando = false;
        })
        .catch((error) => {
          return error;
        });
    },

    editItem(item) {
      this.editedIndex = item.id; //this.categorias.indexOf(item)
      if (this.ruta == "categoria") {
        this.editedCategoryItem = Object.assign({}, item);
      } else if (this.ruta == "articulo") {
        this.editedArticleItem = Object.assign({}, item);
      } else {
        this.editedUserItem = Object.assign({}, item);
      }

      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = item.id; //this.categorias.indexOf(item)
      if (this.ruta == "categoria") {
        this.editedCategoryItem = Object.assign({}, item);
      } else if (this.ruta == "articulo") {
        this.editedArticleItem = Object.assign({}, item);
      } else {
        this.editedUserItem = Object.assign({}, item);
      }
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.informacion.splice(this.editedIndex, 1);
      this.closeDelete();
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        //this.editedItem = Object.assign({}, this.defaultItem)
        if (this.ruta == "categoria") {
          this.editedCategoryItem = Object.assign({}, this.defaultCategoryItem);
        } else if (this.ruta == "articulo") {
          this.editedArticleItem = Object.assign({}, this.defaultArticleItem);
        } else {
          this.editedUserItem = Object.assign({}, this.defaultUserItem);
        }
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        if (this.ruta == "categoria") {
          this.editedCategoryItem = Object.assign({}, this.defaultCategoryItem);
        } else if (this.ruta == "articulo") {
          this.editedArticleItem = Object.assign({}, this.defaultArticleItem);
        } else {
          this.editedUserItem = Object.assign({}, this.defaultUserItem);
        }
        this.editedIndex = -1;
      });
    },

    save() {
      const objetoEditado = new Object();
      if (this.ruta == 'categoria') {
          objetoEditado.id = this.editedCategoryItem.id;
          objetoEditado.nombre = this.editedCategoryItem.nombre;
          objetoEditado.descripcion = this.editedCategoryItem.descripcion;
        } else if(this.ruta == 'articulo') {
          objetoEditado.id = this.editedArticleItem.id;
          objetoEditado.nombre = this.editedArticleItem.nombre;
          objetoEditado.descripcion = this.editedArticleItem.descripcion;
          objetoEditado.codigo = this.editedArticleItem.codigo;
          objetoEditado.precio_venta = this.editedArticleItem.precio_venta;
          objetoEditado.stock = this.editedArticleItem.stock;
        }else{
          objetoEditado.id= this.editedUserItem.id;
          objetoEditado.nombre= this.editedUserItem.nombre;
          objetoEditado.rol= this.editedUserItem.rol;
          objetoEditado.email= this.editedUserItem.email;
          objetoEditado.tipo_documento= this.editedUserItem.tipo_documento;
          objetoEditado.num_documento= this.editedUserItem.num_documento;
          objetoEditado.direccion= this.editedUserItem.direccion;
          objetoEditado.telefono= this.editedUserItem.telefono;
        }
      if (this.editedIndex > -1) {
        
          axios.put(`http://localhost:3000/api/${this.ruta}/update`, objetoEditado)
          .then((response) => {
            console.log(response);
            this.list(this.ruta);
            objetoEditado = new Object();
          })
          .catch((error) => {
            console.log(error);
            return error;
          });
        
        // Object.assign(this.desserts[this.editedIndex], this.editedItem)
      } else {
        //agregar un objeto
        
        if (this.ruta == 'categoria') {
          objetoEditado.id = this.editedCategoryItem.id;
          objetoEditado.nombre = this.editedCategoryItem.nombre;
          objetoEditado.descripcion = this.editedCategoryItem.descripcion;
        } else if(this.ruta == 'articulo') {
          objetoEditado.id = this.editedArticleItem.id;
          objetoEditado.nombre = this.editedArticleItem.nombre;
          objetoEditado.descripcion = this.editedArticleItem.descripcion;
          objetoEditado.codigo = this.editedArticleItem.codigo;
          objetoEditado.precio_venta = this.editedArticleItem.precio_venta;
          objetoEditado.stock = this.editedArticleItem.stock;
        }else{
          objetoEditado.id= this.editedUserItem.id;
          objetoEditado.nombre= this.editedUserItem.nombre;
          objetoEditado.rol= this.editedUserItem.rol;
          objetoEditado.email= this.editedUserItem.email;
          objetoEditado.tipo_documento= this.editedUserItem.tipo_documento;
          objetoEditado.num_documento= this.editedUserItem.num_documento;
          objetoEditado.direccion= this.editedUserItem.direccion;
          objetoEditado.telefono= this.editedUserItem.telefono;
        }
        //añadir objeto
        axios.post(`http://localhost:3000/api/${this.ruta}/add`, objetoEditado)//añadir los objetos con los if
          .then((response) => {
            console.log(response);
            this.list(this.ruta);
            objetoEditado = new Object();
          })
          .catch((error) => {
            console.log(error);
            return error;
          });
        //this.desserts.push(this.editedItem)
      }
      this.close();
    },
  },
};
</script>

<style scoped>
</style>