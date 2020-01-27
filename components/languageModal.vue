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
import LanguageObject from '@/models/Language.json'

export default {
  data: () => ({
    // dialog: false,
    editedItem: LanguageObject,
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
  methods: {
    save() {
      if (this.$refs.form.validate()) {
        if (this.editedIndex > -1) {
          Object.assign(this.items[this.editedIndex], this.editedItem)
        } else {
          this.$http
            .post('/language/', JSON.stringify(this.editedItem), {
              headers: { 'Content-Type': 'application/json; charset=utf-8' }
            })
            .catch((err) => console.log(err))
        }
        this.close()
      }
    },
    close() {
      this.$emit('update-show-modal', false)
      setTimeout(() => {
        this.editedItem = Object.assign({}, LanguageObject)
        this.editedIndex = -1
      }, 300)
    }
  }
}
</script>

<style></style>
