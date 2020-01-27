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
            <v-toolbar-title>D&D 5e - Languages</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="1024">
              <template v-slot:activator="{ on }">
                <v-btn v-on="on" color="primary" dark class="mb-2">
                  New Language
                </v-btn>
              </template>
              <LanguageModal @update-show-modal="updateModalShow" />
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
            <div>Atttuning with an helm of comprehend languages...</div>
          </v-card>
        </template>

        <!-- The Table -->
        <template v-slot:item="{ item }">
          <tr style="cursor: pointer;">
            <td
              v-if="$vuetify.breakpoint.smAndUp"
              class="text-center d-block d-sm-table-cell"
            ></td>
            <td class="text-left d-block d-sm-table-cell">
              <span>{{ item.name }}</span>
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
import LanguageModal from '@/components/languageModal'
export default {
  components: {
    LanguageModal
  },

  authenticated: true,

  data: () => ({
    dialog: false,
    editedIndex: -1,
    loading: true,
    headers: null,
    filter: {
      name: ''
    },
    rules: {
      required: (value) => !!value || 'Required.',
      counter: (value) => value.length <= 20 || 'Max 20 characters'
    }
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'New Language' : 'Edit Language'
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
        { text: 'Actions', value: 'action', align: 'center', sortable: false }
      ]
    },

    initialize() {
      this.getData()
      this.headers = this.getHeaders()
    },

    getData() {
      this.$http.get('/language/').then((res) => {
        const returned = res.data._embedded.language
        this.items = Object.freeze(returned)
        this.filter.items = this.items
        this.loading = false
      })
    },

    doFilter() {
      this.filter.items = this.items.filter((i) => {
        return (
          !this.filter.name || i.name.toLowerCase().includes(this.filter.name)
        )
      })
    },

    updateModalShow(show) {
      this.dialog = show
      if (!show) {
        this.getData()
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

.v-expansion-panel {
  background-color: #eeeeee !important;
}

.v-btn--active {
  background-color: blue-grey !important;
}
</style>
