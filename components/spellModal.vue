<template>
  <v-card>
    <v-form ref="form">
      <v-card-title>
        <span class="headline">{{ formTitle }}</span>
      </v-card-title>

      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12" sm="6" md="4">
              <v-text-field
                :rules="[rules.required]"
                v-model="editedItem.name"
                label="Name"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="2">
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
                :rules="[rules.required]"
                v-model="editedItem.level"
                item-value="id"
                item-text="name"
              ></v-select>
            </v-col>
            <v-col ols="12" sm="6" md="4">
              <v-select
                label="School"
                :items="filter.schools"
                :rules="[rules.required]"
                v-model="editedItem.school"
                item-value="id"
                item-text="name"
              >
              </v-select>
            </v-col>
            <v-col cols="12" sm="6" md="2" class="d-block">
              <v-switch
                v-model="editedItem.isConcentration"
                label="Concentration"
              />
              <v-switch
                v-model="editedItem.isRitual"
                label="Ritual"
                class="m"
              />
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12" sm="6" md="4" class="d-flex flex-row">
              <v-text-field
                v-model="editedItem.castingValue"
                label="Casting Time"
              ></v-text-field>
              <v-select
                :items="filter.castingTimes"
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
                label="Range"
                :items="filter.ranges"
                :rules="[rules.required]"
                v-model="editedItem.range"
                item-value="id"
                item-text="name"
                class="mr-2"
              >
              </v-select>
              <v-text-field
                label="Distance (ft.)"
                v-model="editedItem.rangeDistance"
                :disabled="editedItem.range == null || editedItem.range != 3"
              ></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4" class="d-flex flex-row">
              <v-select
                label="Duration"
                :items="filter.durations"
                :rules="[rules.required]"
                v-model="editedItem.duration"
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
                :items="filter.durationTypes"
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
            <v-col cols="12" sm="6" md="3">
              <p>Components</p>
              <v-btn-toggle dense multiple>
                <v-btn
                  @click="editedItem.isVisual = !editedItem.isVisual"
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
            <v-col cols="12" sm="6" md="4">
              <v-text-field
                v-model="editedItem.components"
                :disabled="!editedItem.isMaterial"
                label="Material Component Description"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="6" md="4" class="d-flex flex-row">
              <v-select
                label="Source"
                :items="filter.sources"
                :rules="[rules.required]"
                v-model="editedItem.source"
                item-value="id"
                item-text="name"
                class="mr-2"
              ></v-select>
            </v-col>
            <v-col cols="12" sm="6" md="1" class="d-flex flex-row">
              <v-text-field
                label="Page"
                v-model="editedItem.sourcePage"
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
      <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
      <v-btn color="blue darken-1" text @click="save">Save</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import SpellObject from '@/models/Spell.json'

export default {
  created() {
    console.log(this.editedItem)
  },
  data: () => ({
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
  }
}
</script>

<style></style>
