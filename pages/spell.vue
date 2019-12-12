<template>
  <v-layout>
    <v-flex class="text-center">
      <v-data-table
        :headers="headers"
        :items="items"
        show-expand
        item-key="name"
        sort-by="name"
        :loading="loading"
        disable-pagination
        hide-default-footer
        class="elevation-1"
      >
        <template v-slot:progress class="text-center">
          <v-progress-linear
            color="blue-grey"
            :height="8"
            indeterminate
          ></v-progress-linear>
          <v-card class="d-flex pa-2 justify-center" row>
            <div>Searching in the Spellbook...</div>
          </v-card>
        </template>
        <template v-slot:expanded-item="{ headers }">
          <td :colspan="headers.length">Peek-a-boo!</td>
        </template>
        <template v-slot:body="{ items }">
          <tbody>
            <tr v-for="item in items" :key="item.name">
              <td>{{ item.id }}</td>
              <td class="text-left">{{ item.name }}</td>
              <td class="text-center">
                {{ item.level == 0 ? 'Cantrip' : item.level }}
              </td>
              <td class="text-center">{{ item.school.name }}</td>
              <td class="text-center">
                {{ item.castingValue }} {{ item.castingTime.name
                }}{{ item.castingValue > 1 ? 's' : '' }}
              </td>
              <td>{{ item.range.name }}</td>
              <td>{{ item.duration ? item.duration.name : '' }}</td>
              <!-- <td class="text-center">{{ item.isVerbal ? 'Yes' : 'No' }}</td>
              <td class="text-center">{{ item.isSomatic ? 'Yes' : 'No' }}</td>
              <td class="text-center">
                {{ item.isMaterial ? 'Yes' : 'No' }}
              </td> -->
              <td class="text-center">
                <v-chip
                  class="ma-0"
                  x-small
                  :color="item.isSomatic ? 'blue-grey' : 'grey lighten-2'"
                  text-color="white"
                >
                  <v-tooltip bottom eager>
                    <template v-slot:activator="{ on }">
                      <span v-on="on">S</span>
                    </template>
                    <span>Somatic</span>
                  </v-tooltip>
                </v-chip>
                <v-chip
                  class="ma-0"
                  x-small
                  :color="item.isVerbal ? 'blue-grey' : 'grey lighten-2'"
                  text-color="white"
                >
                  <v-tooltip bottom eager>
                    <template v-slot:activator="{ on }">
                      <span v-on="on">V</span>
                    </template>
                    <span>Verbal</span>
                  </v-tooltip>
                </v-chip>
                <v-chip
                  class="ma-0"
                  x-small
                  :color="item.isMaterial ? 'blue-grey' : 'grey lighten-2'"
                  text-color="white"
                >
                  <v-tooltip bottom eager>
                    <template v-slot:activator="{ on }">
                      <span v-on="on">M</span>
                    </template>
                    <span
                      >Components{{
                        item.isMaterial ? ': ' + item.components : ''
                      }}</span
                    >
                  </v-tooltip>
                  <!-- <span v-else>M</span> -->
                </v-chip>
              </td>
              <td class="text-center">{{ item.isRitual ? 'Yes' : 'No' }}</td>
              <td class="text-center">
                {{ item.isConcentration ? 'Yes' : 'No' }}
              </td>
              <td class="text-xs-right">
                {{ item.source.name }}
              </td>
            </tr>
          </tbody>
        </template>
        <template v-slot:no-data>
          Sorry, There is no data do display.
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>

<script>
export default {
  authenticated: true,
  data: () => ({
    loading: true,
    expand: false,
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
    rules: {
      required: (value) => !!value || 'Required.',
      counter: (value) => value.length <= 20 || 'Max 20 characters'
    }
  }),

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
        { text: 'Level', value: 'level', align: 'center' },
        { text: 'School', value: 'school.name', align: 'center' },
        { text: 'Casting', value: 'castingTime.name', align: 'center' },
        { text: 'Range', value: 'range.name', align: 'center' },
        { text: 'Duration', value: 'duration.name', align: 'center' },
        // { text: 'Verbal', value: 'isVerbal', align: 'center' },
        // { text: 'Somatic', value: 'isSomatic', align: 'center' },
        // { text: 'Material', value: 'isMaterial', align: 'center' },
        { text: 'Components', value: 'isMaterial', align: 'center' },
        { text: 'Ritual', value: 'isRitual', align: 'center' },
        { text: 'Concentration', value: 'isConcentration', align: 'center' },
        { text: 'Source', value: 'source.name', align: 'center' },
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
      this.$http.get('/spell/').then((res) => {
        console.log(res)
        this.items = res.data._embedded.spell // data.content
        this.loading = false
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
  }
}
</script>
