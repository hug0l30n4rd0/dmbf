<template>
  <v-layout>
    <v-flex class="text-center">
      <v-expansion-panels color="grey">
        <v-expansion-panel>
          <v-expansion-panel-header>
            <b>FILTERS</b>
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-row>
              <v-col>
                <v-text-field v-model="filter.name" label="Name"></v-text-field>
              </v-col>
              <v-col>
                <v-select
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
                  label="Level"
                  item-value="id"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
              </v-col>
              <v-col>
                <v-select
                  :items="filter.schools"
                  v-model="filter.selectedSchools"
                  label="School"
                  item-value="id"
                  item-text="name"
                  multiple
                  chips
                ></v-select>
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
                <!-- <v-btn-toggle
                  v-model="filter.material"
                  mandatory
                  label="Material"
                >
                  <v-btn :value="false" flat>No</v-btn>
                  <v-btn :value="null" flat>Any</v-btn>
                  <v-btn :value="true" flat>Yes</v-btn>
                </v-btn-toggle> -->
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
                  stallone
                  idae
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
                <v-btn @click="doFilter" color="primary">Filter</v-btn>
              </v-col>
            </v-row>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>

      <v-data-table
        :headers="headers"
        :items="filter.items"
        :loading="loading"
        item-key="id"
        sort-by="name"
        disable-pagination
        hide-default-footer
        show-expand
        class="elevation-1"
      >
        <!-- CRUD Modal -->
        <template v-slot:top>
          <v-toolbar flat color="white">
            <v-toolbar-title>D&D 5e - Spells</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="1024">
              <template v-slot:activator="{ on }">
                <v-btn v-on="on" color="primary" dark class="mb-2"
                  >New Spell</v-btn
                >
              </template>
              <SpellModal
                :schools="filter.schools"
                :castingTimes="filter.castingTimes"
                :ranges="filter.ranges"
                :durations="filter.durations"
                :durationTypes="filter.durationTypes"
                :sources="filter.sources"
                :gameClasses="filter.gameClasses"
                @update-show-modal="updateSpellModalShow"
              ></SpellModal>
            </v-dialog>
          </v-toolbar>
        </template>

        <!-- Progress -->
        <template v-slot:progress class="text-center">
          <v-progress-linear
            :height="8"
            color="blue-grey"
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
                  <v-col v-if="$vuetify.breakpoint.smAndUp" class="text-left">
                    <b>{{ item.school.name }}</b>
                    <br />Level:
                    <b>{{ displayLevel(item.level) }}</b>
                    <br />Casting time:
                    <b>{{ displayCasting(item) }}</b>
                    <br />Range:
                    <b>{{ displayRange(item) }}</b>
                    <br />Duration:
                    <b>{{ item.level }}</b>
                    <br />
                  </v-col>
                  <v-col
                    :cols="$vuetify.breakpoint.xs ? '12' : '10'"
                    class="text-justify"
                  >
                    <div v-if="$vuetify.breakpoint.xs">
                      Casting:
                      <b>{{ displayCasting(item) }}</b
                      >, Range:
                      <b>{{ displayRange(item) }}</b>
                      <span v-if="item.duration">
                        , Duration:
                        <b>{{ item.duration ? item.duration.name : '' }}</b>
                      </span>
                      <br />
                    </div>
                    Components:
                    <b>{{ displayComponents(item) }}</b>
                    <br />
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
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            ></td>
            <td
              :class="{
                'text-center': $vuetify.breakpoint.xs
              }"
              class="text-left d-block d-sm-table-cell"
            >
              <div v-if="$vuetify.breakpoint.xs">
                <span class="font-weight-black text-uppercase">
                  {{ item.name }}
                </span>
                <br />
                <span
                  >{{ item.school.name }}, {{ displayLevel(item.level) }}</span
                >
                <br />
              </div>
              <span v-else>{{ item.name }}</span>
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            >
              <span>{{ displayLevel(item.level) }}</span>
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            >
              {{ item.school.name }}
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            >
              {{ displayCasting(item) }}
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="d-block d-sm-table-cell"
            >
              {{ displayRange(item) }}
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="d-block d-sm-table-cell"
            >
              {{
                item.duration.id == 4 //Time
                  ? item.durationValue + ' ' + item.durationType.name
                  : item.duration.name
              }}
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            >
              <div class="tooltip">
                <span
                  :class="item.isVerbal ? 'blue-grey' : 'grey lighten-2'"
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
                >
                  <span class="v-chip__content">
                    <span>V</span>
                  </span>
                </span>
                <span class="tooltiptext">Verbal</span>
              </div>

              <div class="tooltip">
                <span
                  :class="item.isSomatic ? 'blue-grey' : 'grey lighten-2'"
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
                >
                  <span class="v-chip__content">
                    <span>S</span>
                  </span>
                </span>
                <span class="tooltiptext">Somatic</span>
              </div>

              <div class="tooltip">
                <span
                  :class="item.isMaterial ? 'blue-grey' : 'grey lighten-2'"
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
                >
                  <span class="v-chip__content">
                    <span>M</span>
                  </span>
                </span>
                <span class="tooltiptext">
                  Components{{ item.isMaterial ? ': ' + item.components : '' }}
                </span>
              </div>
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            >
              {{ item.isRitual ? 'Yes' : 'No' }}
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            >
              {{ item.isConcentration ? 'Yes' : 'No' }}
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            >
              <div
                v-for="gc in item.gameClasses"
                :key="gc.shortname"
                class="tooltip"
              >
                <template v-if="gc != null">
                  <span
                    class="ma-0 v-chip theme--light v-size--x-small white--text blue-grey"
                  >
                    <span class="v-chip__content">
                      <span>{{ gc.shortName }}</span>
                    </span>
                  </span>
                  <span class="tooltiptext">{{ gc.name }}</span>
                </template>
                &nbsp;
              </div>
            </td>
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-xs-right d-block d-sm-table-cell"
            >
              <div class="tooltip">
                <span
                  :class="
                    item.source.isOfficial ? 'blue-grey' : 'lime darken-4'
                  "
                  class="ma-0 v-chip theme--light v-size--x-small white--text"
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
            <td class="text-xs-right d-block d-sm-table-cell">
              <!-- <v-btn v-on="on" color="primary" dark class="mb-2" icon
                >edit</v-btn
              > -->
              <v-btn text icon>
                <v-icon>mdi-pencil</v-icon>
              </v-btn>
            </td>
          </tr>
        </template>

        <template v-slot:no-data>Sorry, There is no data do display.</template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>

<script>
import SpellModal from '@/components/spellModal'
export default {
  components: {
    SpellModal
  },
  authenticated: true,
  data: () => ({
    dialog: false,
    editedIndex: -1,
    loading: true,
    expand: false,
    type: '',
    types: [],
    headers: null,
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
      selectedSchools: [],
      gameClasses: []
    },
    rules: {
      required: (value) => !!value || 'Required.',
      counter: (value) => value.length <= 20 || 'Max 20 characters'
    }
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'New Spell' : 'Edit Spell'
    }
  },

  // watch: {
  //   dialog(val) {
  //     val || this.close()
  //   }
  // },

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
        { text: 'Classes', value: 'gameClasses', align: 'center' },
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
      this.getGameClasses()
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
      this.$http.get('/resource/spell-range/').then((res) => {
        this.filter.ranges = res.data
      })
    },
    getSpellSchools() {
      this.$http.get('/resource/spell-school/').then((res) => {
        this.filter.schools = res.data
      })
    },
    getGameClasses() {
      this.$http.get('/gameClass/').then((res) => {
        this.filter.gameClasses = res.data._embedded.gameClass
      })
    },
    getData() {
      this.$http.get('/spell/').then((res) => {
        const returned = res.data._embedded.spell
        this.items = Object.freeze(returned)
        this.filter.items = this.items
        this.loading = false
      })
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

    updateSpellModalShow(show) {
      this.dialog = show
      if (!show) {
        this.getData()
      }
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

.v-btn--active {
  background-color: blue-grey !important;
}
</style>
