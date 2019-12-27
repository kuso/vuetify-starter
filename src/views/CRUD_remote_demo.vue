<template>
  <v-data-table
    :headers="headers"
    :items="countries"
    sort-by="name"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar flat color="white">
        <v-toolbar-title>Remote CRUD API Demo</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on">New Item</v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.id" label="ID" readonly></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.code" label="Code"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.name" label="Name"></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="save">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.action="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        edit
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">Reset</v-btn>
    </template>
  </v-data-table>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'crud_remote_demo',

    data: () => ({
      dialog: false,
      headers: [
        { text: 'ID', value: 'id' },
        { text: 'Code', value: 'code' },
        { text: 'Name', value: 'name' },
        { text: 'Actions', value: 'action', sortable: false },
      ],
      countries: [],
      editedIndex: -1,
      editedItem: {
        id: 0,
        code: '',
        name: '',
      },
      defaultItem: {
        id: 0,
        code: '',
        name: '',
      },
    }),

    computed: {
      formTitle() {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item';
      },
    },

    watch: {
    },

    created() {
      this.initialize();
    },

    methods: {
      initialize() {
        axios.get('http://127.0.0.1:8080/countries')
          .then((response) => {
            this.countries = response.data;
          })
          .catch((error) => {
            console.log(error);
          });
      },

      editItem(item) {
        this.editedIndex = this.countries.indexOf(item);
        this.editedItem = Object.assign({}, item);
        this.dialog = true;
      },

      deleteItem(item) {
        let url = 'http://127.0.0.1:8080/countries/';
        url = url.concat(item.code);
        axios.delete(url)
          .then((response) => {
            console.log(response);
            const index = this.countries.indexOf(item);
            this.countries.splice(index, 1);
          })
          .catch((error) => {
            console.log(error);
          });
      },

      close() {
        this.dialog = false;
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem);
          this.editedIndex = -1;
        }, 300);
      },

      save() {
        const url = 'http://127.0.0.1:8080/countries';
        axios.post(url, {
          id: this.editedItem.id,
          code: this.editedItem.code,
          name: this.editedItem.name,
        })
          .then((response) => {
            console.log(response.data);
            this.editedItem.id = response.data.id;
            if (this.editedIndex > -1) {
              Object.assign(this.countries[this.editedIndex], this.editedItem);
            } else {
              this.countries.push(this.editedItem);
            }
            this.close();
          })
          .catch((error) => {
            console.log(error);
          });
      },
    },
  };
</script>
