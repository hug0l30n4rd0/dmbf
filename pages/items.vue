<template>
  <v-layout>
    <v-flex class="text-center">
      <v-data-table
        :headers="headers"
        :items="items"
        sort-by="name"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat color="white">
            <v-toolbar-title>MAGIC ITEMS</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="600px">
              <template v-slot:activator="{ on }">
                <v-btn v-on="on" color="primary" dark class="mb-2"
                  >New Item</v-btn
                >
              </template>
              <v-card>
                <v-form ref="form" v-model="valid">
                  <v-card-title>
                    <span class="headline">{{ formTitle }}</span>
                  </v-card-title>

                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="6" md="8">
                          <v-text-field
                            ref="name"
                            v-model="editedItem.name"
                            :rules="[rules.required]"
                            label="Name"
                            required
                          ></v-text-field>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-switch
                            ref="attunement"
                            v-model="editedItem.attunement"
                            class="ma-2"
                            label="Attunement"
                          ></v-switch>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-select
                            ref="type"
                            v-model="editedItem.type"
                            :rules="[rules.required]"
                            :items="types"
                            item-text="name"
                            return-object
                            label="Type"
                          ></v-select>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-select
                            ref="rarity"
                            v-model="editedItem.rarity"
                            :rules="[rules.required]"
                            :items="rarities"
                            item-text="name"
                            return-object
                            label="Rarity"
                          ></v-select>
                        </v-col>
                        <v-col cols="12" sm="6" md="4">
                          <v-switch
                            ref="cursed"
                            v-model="editedItem.cursed"
                            class="ma-2"
                            label="Cursed"
                          ></v-switch>
                        </v-col>
                        <v-col cols="12" sm="6" md="12">
                          <v-textarea
                            ref="description"
                            v-model="editedItem.description"
                            :rules="[rules.required]"
                            outlined
                            label="Description"
                            value=""
                          ></v-textarea>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-card-text>

                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn @click="close" color="blue darken-1" text
                      >Cancel</v-btn
                    >
                    <v-btn @click="save" color="blue darken-1" text>Save</v-btn>
                  </v-card-actions>
                </v-form>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>
        <template v-slot:item.attunement="{ item }">
          {{ item.attunement ? 'Yes' : 'No' }}
        </template>
        <template v-slot:item.cursed="{ item }">
          {{ item.cursed ? 'Yes' : 'No' }}
        </template>
        <template v-slot:item.action="{ item }">
          <v-icon @click="editItem(item)" small class="mr-2">
            mdi-pencil
          </v-icon>
          <v-icon @click="deleteItem(item)" small>
            mdi-delete
          </v-icon>
        </template>

        <template v-slot:no-data>
          Sorry, There is no data do display.
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>

<script>
// import axios from 'axios'
// import ItemRarity from '@/model/ItemRarityMock.json'
// import ItemType from '@/model/ItemTypeMock.json'
// import { required, minLength } from 'vuelidate/lib/validators'

export default {
  authenticated: true,
  data: () => ({
    rarity: null,
    rarities: [],
    type: '',
    types: [],
    attunement: false,
    cursed: false,
    dialog: false,
    headers: null,
    desserts: [],
    items: [],
    editedIndex: -1,
    formHasErrors: false,
    valid: true,
    editedItem: {
      name: '',
      type: null,
      rarity: null,
      attunement: false,
      cursed: false,
      description: ''
    },
    // defaultItem: {
    //   name: '',
    //   type: null,
    //   rarity: null,
    //   attunement: false,
    //   cursed: false,
    //   description: ''
    // }
    rules: {
      required: (value) => !!value || 'Required.',
      counter: (value) => value.length <= 20 || 'Max 20 characters'
    }
  }),

  // validations: {
  //   name: {
  //     required,
  //     minLength: minLength(4)
  //   }
  // },

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
    },
    form() {
      return {
        name: this.name,
        attunement: this.attunement,
        cursed: this.cursed,
        type: this.type,
        rarity: this.rarity,
        description: this.description
      }
    }
  },

  watch: {
    dialog(val) {
      val || this.close()
    }
  },

  created() {
    this.initialize()
  },

  methods: {
    getHeaders() {
      return [
        {
          text: 'Name',
          align: 'left',
          sortable: true,
          value: 'name'
        },
        { text: 'Type', value: 'type.name', align: 'center' },
        { text: 'Rarity', value: 'rarity.name', align: 'center' },
        { text: 'Attunement', value: 'attunement', align: 'center' },
        { text: 'Cursed', value: 'cursed', align: 'center' },
        { text: 'Actions', value: 'action', align: 'center', sortable: false }
      ]
    },

    getItemTypes() {
      this.$http.get('/resource/item-type/').then((res) => {
        this.types = res.data
      })
    },

    getItemRarities() {
      this.$http.get('/resource/item-rarity/').then((res) => {
        this.rarities = res.data
      })
    },

    getData() {
      this.$http.get('/item/').then((res) => {
        console.log(res)
        this.items = res.data._embedded.item // data.content
      })
    },

    initialize() {
      this.getItemTypes()
      this.getItemRarities()
      this.getData()
      this.headers = this.getHeaders()
    },

    editItem(item) {
      this.editedIndex = this.items.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      const index = this.items.indexOf(item)
      confirm('Are you sure you want to delete this item?') &&
        this.items.splice(index, 1)
    },

    close() {
      this.dialog = false
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      }, 300)
    },

    save() {
      if (this.$refs.form.validate()) {
        if (this.editedIndex > -1) {
          Object.assign(this.items[this.editedIndex], this.editedItem)
        } else {
          console.log(JSON.stringify(this.editedItem))

          this.$http
            .post('/item/', JSON.stringify(this.editedItem), {
              headers: { 'Content-Type': 'application/json; charset=utf-8' }
            })
            .then((res) => {
              console.log('salvo com sucesso')
              this.getData()
            })
            .catch((err) => console.log(err))
        }
        this.close()
      }
    }

    // validateForm() {
    //   if (!(this.name || this.description)) {
    //      e.preventDefault();
    //   }
    // }
  }

  // computed: {
  //   form () {
  //     return {
  //       name: this.name,
  //       attunement: this.attunement,
  //       cursed: this.cursed,
  //       type: this.type,
  //       rarity: this.rarity,
  //       description: this.description,
  //     }
  //   },
  // },
}
</script>
>
