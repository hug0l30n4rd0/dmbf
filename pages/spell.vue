<template>
  <v-layout>
    <v-flex class="text-center">
      <v-expansion-panels color="grey">
        <v-expansion-panel>
          <v-expansion-panel-header><b>FILTERS</b></v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-row>
              <v-col>
                <v-text-field label="Name" v-model="filter.name"></v-text-field>
              </v-col>
              <v-col>
                <v-select
                  label="Level"
                  :items="[
                    { id: 0, name: 'Cantrip' },
                    { id: 1, name: '1st' },
                    { id: 2, name: '2nd' },
                    { id: 3, name: '3rd' },
                    { id: 4, name: '4th' },
                    { id: 5, name: '5th' },
                    { id: 6, name: '6th' },
                    { id: 7, name: '7th' },
                    { id: 8, name: '8th' },
                    { id: 9, name: '9th' }
                  ]"
                  v-model="filter.selectedLevels"
                  item-value="id"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  label="School"
                  :items="filter.schools"
                  v-model="filter.selectedSchools"
                  item-value="id"
                  item-text="name"
                  multiple
                  chips
                >
                </v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="filter.castingTimes"
                  v-model="filter.selectedCastingTimes"
                  label="Casting"
                  item-value="id"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="filter.ranges"
                  v-model="filter.selectedRanges"
                  label="Range"
                  item-value="id"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-select
                  label="Class"
                  item-text="name"
                  multiple
                  chips
                  return-object
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="[
                    { name: 'Yes', value: true },
                    { name: 'No', value: false }
                  ]"
                  v-model="filter.verbal"
                  label="Verbal"
                  item-value="value"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="[
                    { name: 'Yes', value: true },
                    { name: 'No', value: false }
                  ]"
                  v-model="filter.somatic"
                  label="Somatic"
                  item-value="value"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="[
                    { name: 'Yes', value: true },
                    { name: 'No', value: false }
                  ]"
                  v-model="filter.material"
                  label="Material"
                  item-value="value"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="[
                    { name: 'Yes', value: true },
                    { name: 'No', value: false }
                  ]"
                  v-model="filter.ritual"
                  label="Ritual"
                  item-value="value"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="[
                    { name: 'Yes', value: true },
                    { name: 'No', value: false }
                  ]"
                  v-model="filter.concentration"
                  label="Concentration"
                  item-value="value"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="filter.sources"
                  v-model="filter.selectedSources"
                  label="Source"
                  item-value="cod"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-btn color="default">Clear</v-btn>
                <v-btn color="primary" @click="doFilter">Filter</v-btn>
              </v-col>
            </v-row>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>

      <v-data-table
        :headers="headers"
        :items="filter.items"
        :loading="loading"
        item-key="cod"
        sort-by="name"
        disable-pagination
        hide-default-footer
        show-expand
        class="elevation-1"
      >
        <!-- CRUD Modal -->
        <template v-slot:top>
          <v-toolbar flat color="white">
            <v-toolbar-title>My CRUD</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on }">
                <v-btn color="primary" dark class="mb-2" v-on="on"
                  >New Item</v-btn
                >
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.name"
                          label="Dessert name"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.calories"
                          label="Calories"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.fat"
                          label="Fat (g)"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.carbs"
                          label="Carbs (g)"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.protein"
                          label="Protein (g)"
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close"
                    >Cancel</v-btn
                  >
                  <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>

        <!-- Progress -->
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

        <!-- Expanded Card -->
        <template v-slot:expanded-item="{ headers, item }">
          <!-- Full Width-->
          <td :colspan="headers.length">
            <div
              class="v-card v-sheet theme--light"
              default="default"
              subtitle="true"
              supportingtext="true"
            >
              <div class="v-card__title" style="background-color: #CCC;">
                {{ item.name }}
              </div>
              <div class="v-card__text">
                <v-row>
                  <v-col
                    class="text-l
            i.school.id === this.filterSchools.idy.breakpoint.smAndUp"
                  >
                    <b>{{ item.school.name }}</b> <br />
                    Level: <b>{{ displayLevel(item.level) }}</b> <br />
                    Casting time:
                    <b>{{ displayCasting(item) }}</b>
                    <br />
                    Range:
                    <b>{{ displayRange(item) }}</b>
                    <br />

                    Duration: <b>{{ item.level }}</b> <br />
                  </v-col>
                  <v-col class="text-justify">
                    <div v-if="$vuetify.breakpoint.xs">
                      Casting: <b>{{ displayCasting(item) }}</b
                      >, Range: <b>{{ displayRange(item) }}</b
                      ><span v-if="item.duration"
                        >, Duration:
                        <b>{{ item.duration ? item.duration.name : '' }}</b>
                      </span>
                      <br />
                    </div>
                    Components: <b>{{ displayComponents(item) }}</b> <br />
                    <hr />
                    <br />
                    {{ item.description }}
                  </v-col>
                </v-row>
              </div>
            </div>
          </td>
        </template>

        <!-- The Table -->
        <template v-slot:item="{ item, expand, isExpanded }">
          <tr @click="expand(!isExpanded)" style="cursor: pointer;">
            <td
              class="text-center d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            ></td>
            <td
              class="text-left d-block d-sm-table-cell"
              :class="{
                'text-center': $vuetify.breakpoint.xs
              }"
            >
              <div v-if="$vuetify.breakpoint.xs">
                <span class="font-weight-black text-uppercase">{{
                  item.name
                }}</span>
                <br />
                <span>
                  {{ item.school.name }}, {{ displayLevel(item.level) }}</span
                >
                <br />
              </div>
              <span v-else>{{ item.name }}</span>
            </td>
            <td
              class="text-center d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
              <span>{{ displayLevel(item.level) }}</span>
            </td>
            <td
              class="text-center d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
              {{ item.school.name }}
            </td>
            <td
              class="text-center d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
              {{ displayCasting(item) }}
            </td>
            <td
              class="d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
              {{ displayRange(item) }}
            </td>
            <td
              class="d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
              {{ item.duration ? item.duration.name : '' }}
            </td>
            <td
              class="text-center d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
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
            <td
              class="text-center d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
              {{ item.isRitual ? 'Yes' : 'No' }}
            </td>
            <td
              class="text-center d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
              {{ item.isConcentration ? 'Yes' : 'No' }}
            </td>
            <td
              class="text-xs-right d-block d-sm-table-cell"
              v-if="$vuetify.breakpoint.smAndUp"
            >
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
          </tr>
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
    items: [],
    filter: {
      items: [],
      name: '',
      verbal: [],
      somatic: [],
      material: [],
      ritual: [],
      concentration: [],
      selectedLevels: [],
      sources: [],
      selectedSources: [],
      castingTimes: [],
      selectedCastingTimes: [],
      durations: [],
      selectedDurations: [],
      durationTypes: [],
      selectedDurationTypes: [],
      ranges: [],
      selectedRanges: [],
      schools: [],
      selectedSchools: []
    },
    editedItem: {
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
        { text: 'Components', value: 'isMaterial', align: 'center' },
        { text: 'Ritual', value: 'isRitual', align: 'center' },
        { text: 'Concentration', value: 'isConcentration', align: 'center' },
        { text: 'Source', value: 'source.name', align: 'center' },
        { text: 'Actions', value: 'action', align: 'center', sortable: false }
      ]
    },

    initialize() {
      this.getSources()
      this.getSpellCastingTimes()
      this.getSpellDurations()
      this.getSpellDurationTypes()
      this.getSpellRanges()
      this.getSpellSchools()
      this.getData()
      this.headers = this.getHeaders()
    },

    getSources() {
      this.$http.get('/source/').then((res) => {
        this.filter.sources = res.data._embedded.source
      })
    },
    getSpellCastingTimes() {
      this.$http.get('/resource/spell-casting-time/').then((res) => {
        this.filter.castingTimes = res.data
      })
    },
    getSpellDurations() {
      this.$http.get('/resource/spell-duration/').then((res) => {
        this.filter.durations = res.data
      })
    },
    getSpellDurationTypes() {
      this.$http.get('/resource/spell-duration-type/').then((res) => {
        this.filter.durationTypes = res.data
      })
    },
    getSpellRanges() {
      this.$http.get('/resourverbalce/spell-range/').then((res) => {
        this.filter.ranges = res.data
      })
    },
    getSpellSchools() {
      this.$http.get('/resource/spell-school/').then((res) => {
        this.filter.schools = res.data
      })
    },

    getData() {
      this.$http.get('/spell/').then((res) => {
        console.log(res)
        const returned = res.data._embedded.spell // data.content
        this.items = Object.freeze(returned)
        this.filter.items = this.items
        this.loading = false
      })
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
    },

    doFilter() {
      this.filter.items = this.items.filter((i) => {
        // console.log('castTime: ' + i.castingTime.id)
        return (
          (!this.filter.name ||
            i.name.toLowerCase().includes(this.filter.name)) &&
          (this.filter.selectedLevels.length === 0 ||
            this.filter.selectedLevels.filter((schoolLvl) => {
              return schoolLvl === i.level
            }).length > 0) &&
          (this.filter.selectedSchools.length === 0 ||
            this.filter.selectedSchools.filter((schoolId) => {
              return schoolId === i.school.id
            }).length > 0) &&
          (this.filter.selectedCastingTimes.length === 0 ||
            this.filter.selectedCastingTimes.filter((castingId) => {
              return castingId === i.castingTime.id
            }).length > 0) &&
          (this.filter.selectedRanges.length === 0 ||
            this.filter.selectedRanges.filter((rangeId) => {
              return rangeId === i.range.id
            }).length > 0) &&
          (this.filter.verbal.length === 0 ||
            this.filter.verbal.length === 2 ||
            this.filter.verbal[0] === i.isVerbal) &&
          (this.filter.somatic.length === 0 ||
            this.filter.somatic.length === 2 ||
            this.filter.somatic[0] === i.isSomatic) &&
          (this.filter.material.length === 0 ||
            this.filter.material.length === 2 ||
            this.filter.material[0] === i.isMaterial) &&
          (this.filter.concentration.length === 0 ||
            this.filter.concentration.length === 2 ||
            this.filter.concentration[0] === i.isConcentration) &&
          (this.filter.ritual.length === 0 ||
            this.filter.ritual.length === 2 ||
            this.filter.ritual[0] === i.isRitual) &&
          (this.filter.selectedSources.length === 0 ||
            this.filter.selectedSources.filter((sourceId) => {
              return sourceId === i.source.cod
            }).length > 0)
        )
      })
    },

    displayLevel(level) {
      if (level === 0) {
        return 'Cantrip'
      } else if (level === 1) {
        return '1st Level'
      } else if (level === 2) {
        return '2nd Level'
      } else if (level === 3) {
        return '3rd Level'
      } else {
        return level + 'th Level'
      }
    },

    displayCasting(item) {
      let retorno = ''
      if (item.castingValue > 1) {
        retorno = item.castingValue + ' '
      }
      retorno += item.castingTime.name
      if (item.castingValue > 1) {
        retorno += 's'
      }
      return retorno
    },

    displayRange(item) {
      let retorno = ''
      if (item.range.id === 3) {
        retorno = item.rangeDistance + ' ' + item.rangeMetric.name
      } else {
        retorno = item.range.name
      }
      return retorno
    },

    displayComponents(item) {
      let retorno = ''
      let appendComma = false
      if (item.isVerbal) {
        appendComma = true
        retorno += 'V'
      }
      if (item.isSomatic) {
        appendComma = true
        if (appendComma) {
          retorno += ', '
        }
        retorno += 'S'
      }
      if (item.isMaterial) {
        appendComma = true
        if (appendComma) {
          retorno += ', '
        }
        retorno += 'M'
        if (item.components) {
          retorno += ' (' + item.components + ')'
        }
      }
      return retorno
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

.v-expansion-panel {
  background-color: #eeeeee !important;
}
</style>
