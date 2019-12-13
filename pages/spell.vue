<template>
  <v-layout>
    <v-flex class="text-center">
      <v-data-table
        :headers="headers"
        :items="items"
        item-key="cod"
        sort-by="name"
        :loading="loading"
        disable-pagination
        hide-default-footer
        show-expand
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
        <template v-slot:expanded-item="{ headers, item }">
          <td :colspan="headers.length">
            <div
              class="v-card v-sheet theme--light"
              default="default"
              subtitle="true"
              supportingtext="true"
            >
              <!---->
              <div class="v-card__title" style="background-color: #CCC;">
                {{ item.name }}
              </div>
              <!-- <div class="v-card__subtitle">{{ item.school.name }}</div> -->
              <div class="v-card__text">
                <v-row>
                  <v-col
                    class="text-left"
                    cols="2"
                    style="background-color: #999;"
                  >
                    <!-- {{ item.name }} -->
                    {{ item.school.name }}
                    Level: <b>{{ item.level }}</b> <br />
                    Casting time: <b>{{ item.level }}</b> <br />
                    Range: <b>{{ item.level }}</b> <br />
                    Components: <b>{{ item.level }}</b> <br />
                    Duration: <b>{{ item.level }}</b> <br />
                  </v-col>
                  <v-col class="justify">
                    {{ item.description }}
                  </v-col>
                </v-row>
              </div>
            </div>
          </td>
        </template>
        <template v-slot:item="{ item, expand, isExpanded }">
          <tr @click="expand(!isExpanded)">
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
            <td>
              {{
                item.range.id == 3
                  ? item.rangeDistance + ' ' + item.rangeMetric.name
                  : item.range.name
              }}
            </td>
            <td>{{ item.duration ? item.duration.name : '' }}</td>
            <td class="text-center">
              <div class="tooltip">
                <span
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
                  :class="item.isSomatic ? 'blue-grey' : 'grey lighten-2'"
                >
                  <span class="v-chip__content">
                    <span>S</span>
                  </span>
                </span>
                <span class="tooltiptext">Somatic</span>
              </div>

              <div class="tooltip">
                <span
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
                  :class="item.isVerbal ? 'blue-grey' : 'grey lighten-2'"
                >
                  <span class="v-chip__content">
                    <span>V</span>
                  </span>
                </span>
                <span class="tooltiptext">Verbal</span>
              </div>

              <div class="tooltip">
                <span
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
                  :class="item.isMaterial ? 'blue-grey' : 'grey lighten-2'"
                >
                  <span class="v-chip__content">
                    <span>M</span>
                  </span>
                </span>
                <span class="tooltiptext"
                  >Components{{
                    item.isMaterial ? ': ' + item.components : ''
                  }}</span
                >
              </div>
            </td>
            <td class="text-center">{{ item.isRitual ? 'Yes' : 'No' }}</td>
            <td class="text-center">
              {{ item.isConcentration ? 'Yes' : 'No' }}
            </td>
            <td class="text-xs-right">
              <div class="tooltip">
                <span
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
                  :class="
                    item.source.isOfficial ? 'blue-grey' : 'lime darken-4'
                  "
                >
                  <span class="v-chip__content">
                    <span>{{ item.source.shortName }}</span>
                  </span>
                </span>
                <span class="tooltiptext"
                  >{{ item.source.name }}, Page {{ item.sourcePage }}</span
                >
              </div>
            </td>
          </tr></template
        >

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
        const returned = res.data._embedded.spell // data.content
        this.items = Object.freeze(returned)
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

<style scoped>
/* Tooltip container */
.tooltip {
  position: relative;
  display: inline-block;
  /*border-bottom: 1px dotted black; /* If you want dots under the hoverable text */
}

/* Tooltip text */
.tooltip .tooltiptext {
  visibility: hidden;
  top: 100%;
  left: 50%;
  width: 240px;
  margin-left: -120px;
  background-color: rgba(0, 0, 0, 0.87);
  color: #fff;
  text-align: center;
  padding: 5px 5px;
  border-radius: 6px;

  /* Position the tooltip text - see examples below! */
  position: absolute;
  z-index: 1;
}

/* Show the tooltip text when you mouse over the tooltip container */
.tooltip:hover .tooltiptext {
  visibility: visible;
}
</style>
