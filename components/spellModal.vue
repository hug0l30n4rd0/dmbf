<template>
  <v-card>
    <v-form ref="form">
      <v-card-title>
        <span class="headline">{{ formTitle }}</span>
      </v-card-title>

      <v-card-text>
        <v-container>
          <v-row>
            <v-col sm="6" md="3">
              <v-text-field
                :rules="[rules.required]"
                v-model="editedItem.name"
                label="Name"
              ></v-text-field>
            </v-col>
            <v-col sm="6" md="2">
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
                v-model="editedItem.level"
                label="Level"
                item-value="id"
                item-text="name"
              ></v-select>
            </v-col>
            <v-col sm="6" md="3">
              <v-select
                :items="schools"
                v-model="editedItem.school"
                label="School"
                item-value="id"
                item-text="name"
              >
              </v-select>
            </v-col>
            <v-col cols="12" sm="6" md="3">
              <p>Components</p>
              <v-btn-toggle dense multiple>
                <v-btn
                  @click="editedItem.isVerbal = !editedItem.isVerbal"
                  class="groupSearchType"
                  >V</v-btn
                >
                <v-btn
                  @click="editedItem.isSomatic = !editedItem.isSomatic"
                  class="groupSearchType"
                  >S</v-btn
                >
                <v-btn
                  @click="editedItem.isMaterial = !editedItem.isMaterial"
                  class="groupSearchType"
                  >M</v-btn
                >
              </v-btn-toggle>
            </v-col>
          </v-row>
          <v-row>
            <v-col sm="12" md="8">
              <v-select
                :items="gameClasses"
                :rules="[rules.required]"
                v-model="editedItem.gameClasses"
                label="Classes"
                item-text="name"
                return-object
                multiple
              >
              </v-select>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <v-text-field
                v-model="editedItem.components"
                :disabled="!editedItem.isMaterial"
                label="Material Component Description"
              ></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12" sm="6" md="4" class="d-flex flex-row">
              <v-text-field
                v-model="editedItem.castingValue"
                label="Casting Time"
              ></v-text-field>
              <v-select
                :items="castingTimes"
                :rules="[rules.required]"
                v-model="editedItem.castingTime"
                item-value="id"
                item-text="name"
                class="ml-2"
              >
              </v-select>
            </v-col>
            <v-col cols="12" sm="6" md="4" class="d-flex flex-row">
              <v-select
                :items="ranges"
                :rules="[rules.required]"
                v-model="editedItem.range"
                label="Range"
                item-value="id"
                item-text="name"
                class="mr-2"
              >
              </v-select>
              <v-text-field
                v-model="editedItem.rangeDistance"
                :disabled="editedItem.range == null || editedItem.range != 3"
                label="Distance (ft.)"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4" class="d-flex flex-row">
              <v-select
                :items="durations"
                :rules="[rules.required]"
                v-model="editedItem.duration"
                label="Duration"
                item-value="id"
                item-text="name"
                class="mr-2"
              >
              </v-select>
              <v-text-field
                v-model="editedItem.durationValue"
                :disabled="
                  editedItem.duration == null ||
                    !(editedItem.duration == 3 || editedItem.duration == 4)
                "
              ></v-text-field>
              <v-select
                :items="durationTypes"
                :rules="[rules.required ? true : false]"
                :disabled="
                  editedItem.duration == null ||
                    !(editedItem.duration == 3 || editedItem.duration == 4)
                "
                v-model="editedItem.durationType"
                item-value="id"
                item-text="name"
                class="ml-2"
              >
              </v-select>
            </v-col>
          </v-row>

          <v-row>
            <v-col sm="6" md="2">
              <v-switch
                v-model="editedItem.isConcentration"
                label="Concentration"
              />
            </v-col>
            <v-col sm="6" md="2">
              <v-switch
                v-model="editedItem.isRitual"
                label="Ritual"
                class="m"
              />
            </v-col>

            <v-col cols="12" sm="6" md="4" class="d-flex flex-row">
              <v-select
                :items="sources"
                :rules="[rules.required]"
                v-model="editedItem.source"
                label="Source"
                item-value="id"
                item-text="name"
                class="mr-2"
              ></v-select>
            </v-col>
            <v-col cols="12" sm="6" md="1" class="d-flex flex-row">
              <v-text-field
                v-model="editedItem.sourcePage"
                label="Page"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
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
    </v-form>

    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn @click="close" color="blue darken-1" text>Cancel</v-btn>
      <v-btn @click="save" color="blue darken-1" text>Save</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import SpellObject from '@/models/Spell.json'

export default {
  props: {
    // showing: {
    //   type: Boolean,
    //   default() {
    //     return false
    //   },
    //   required: true
    // },
    schools: {
      type: Array,
      default() {
        return []
      },
      required: true
    },
    castingTimes: {
      type: Array,
      default() {
        return []
      },
      required: true
    },
    ranges: {
      type: Array,
      default() {
        return []
      },
      required: true
    },
    durations: {
      type: Array,
      default() {
        return []
      },
      required: true
    },
    durationTypes: {
      type: Array,
      default() {
        return []
      },
      required: true
    },
    sources: {
      type: Array,
      default() {
        return []
      },
      required: true
    },
    gameClasses: {
      type: Array,
      default() {
        return []
      },
      required: true
    }
  },

  data: () => ({
    // dialog: false,
    editedItem: SpellObject,
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
  created() {
    console.log(this.editedItem)
  },
  methods: {
    save() {
      if (this.$refs.form.validate()) {
        if (this.editedIndex > -1) {
          console.log('~~~~ Edited Item:')
          // console.log(JSON.stringify(this.editedItem))
          Object.assign(this.items[this.editedIndex], this.editedItem)
        } else {
          console.log('~~~~ Inserted Item:')
          console.log(JSON.stringify(this.editedItem))
          this.$http
            .post('/spell/', JSON.stringify(this.editedItem), {
              headers: { 'Content-Type': 'application/json; charset=utf-8' }
            })
            .then((res) => {
              // this.getData()
              console.log('Modal is DONE!')
            })
            .catch((err) => console.log(err))
        }
        this.close()
      }
    },
    // edit(entity) {
    //   this.editedIndex = this.items.indexOf(entity)
    //   this.editedItem = Object.assign({}, entity)
    //   this.showing = true
    // },

    // delete(entity) {
    //   const index = this.items.indexOf(entity)
    //   confirm('Are you sure you want to delete this item?') &&
    //     this.items.splice(index, 1)
    // },

    close() {
      this.$emit('update-show-modal', false)
      setTimeout(() => {
        this.editedItem = Object.assign({}, SpellObject)
        console.log('~~~RESETED editedItem:')
        console.log(this.editedItem)
        this.editedIndex = -1
      }, 300)
    }
  }
}
</script>

<style></style>
