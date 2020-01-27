<template>
  <v-flex>
    <v-row>
      <v-col>
        <v-text-field label="Character Name"></v-text-field>
        <v-text-field label="PLayer Name"></v-text-field>
      </v-col>
      <v-col>
        <v-text-field label="Class"></v-text-field>
        <v-select label="Race"> </v-select>
      </v-col>
      <v-col>
        <v-select label="Background"> </v-select>
        <v-select label="Alignment"> </v-select>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-row>
          <v-text-field label="Strength" value="10"></v-text-field>
          <v-text-field label="Saving throw" value="0"></v-text-field>
        </v-row>
        <v-text-field label="Athletics"></v-text-field>
      </v-col>
      <v-col>
        <v-text-field label="Dexterity"></v-text-field>
        <v-text-field label="Saving throw"></v-text-field>
        <v-text-field label="Acrobatics"></v-text-field>
        <v-text-field label="Sleight of Hands"></v-text-field>
        <v-text-field label="Stealth"></v-text-field>
      </v-col>
      <v-col>
        <v-text-field label="Constitution"></v-text-field>
        <v-text-field label="Saving throw"></v-text-field>
      </v-col>
      <v-col>
        <v-text-field label="Intelligence"></v-text-field>
        <v-text-field label="Saving throw"></v-text-field>
        <v-text-field label="Arcana" small></v-text-field>
        <v-text-field label="History"></v-text-field>
        <v-text-field label="Investigation"></v-text-field>
        <v-text-field label="Nature"></v-text-field>
        <v-text-field label="Religion"></v-text-field>
      </v-col>
      <v-col>
        <v-text-field label="Wisdom"></v-text-field>
        <v-text-field label="Saving throw"></v-text-field>
        <v-text-field label="Animal Handling"></v-text-field>
        <v-text-field label="Insight"></v-text-field>
        <v-text-field label="Medicine"></v-text-field>
        <v-text-field label="Perception"></v-text-field>
        <v-text-field label="Survival"></v-text-field>
      </v-col>
      <v-col>
        <v-text-field label="Charisma"></v-text-field>
        <v-text-field label="Saving throw"></v-text-field>
        <v-text-field label="Deception"></v-text-field>
        <v-text-field label="Intimidation"></v-text-field>
        <v-text-field label="Performance"></v-text-field>
        <v-text-field label="Persuasion"></v-text-field>
      </v-col>
    </v-row>
    <v-row>
      <v-col v-for="ability in abilities" :key="ability.id">
        <v-card>
          <v-card-title class="headline">{{ ability.name }}</v-card-title>

          <v-card-subtitle class="text-center">
            <v-text-field :value="ability.value" dense outlined />
            ({{ ability.mod }})
          </v-card-subtitle>
        </v-card>
      </v-col>
      <!-- <v-col>
        asdasdasd
      </v-col>
      <v-col>
        asdasdasd
      </v-col> -->
    </v-row>
    <v-row>
      <v-col>
        <v-simple-table :dense="true">
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-center" style="width:10%;">Value</th>
                <th class="text-left" style="width:10%;">Trainned</th>
                <th class="text-left" style="width:60%;">Skill</th>
                <th class="text-left" style="width:10%;">Expertise.</th>
                <th class="text-left" style="width:10%;">Misc.</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in skills" :key="item.name">
                <td class="text-center">{{ getSkillValue(item) }}</td>
                <td>
                  <v-simple-checkbox v-model="item.trainned" />
                </td>
                <td>{{ item.name }}</td>
                <td><v-simple-checkbox v-model="item.expertise" /></td>
                <td>
                  <!-- <v-text-field :value="item.misc" dense /> -->
                </td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-col>
      <v-col>
        asdasdasd
      </v-col>
      <v-col>
        asdasdasd
      </v-col>
    </v-row>
  </v-flex>
</template>

<script>
export default {
  data: () => ({
    prof: 2,
    abilities: [
      {
        id: 1,
        name: 'Strength',
        value: 14,
        mod: 2
      },
      {
        id: 2,
        name: 'Dexterity',
        value: 12,
        mod: 1
      },
      {
        id: 3,
        name: 'Constitution',
        value: 13,
        mod: 1
      },
      {
        id: 4,
        name: 'Intelligence',
        value: 8,
        mod: -1
      },
      {
        id: 5,
        name: 'Wisdom',
        value: 12,
        mod: 1
      },
      {
        id: 6,
        name: 'Charisma',
        value: 7,
        mod: -2
      }
    ],
    skills: [
      {
        id: 1,
        ability: 2,
        name: 'Acrobatics',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 2,
        ability: 5,
        name: 'Animal Handling',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 3,
        ability: 4,
        name: 'Arcana',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 4,
        ability: 1,
        name: 'Athletics',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 5,
        ability: 6,
        name: 'Deception',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 6,
        ability: 4,
        name: 'History',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 7,
        ability: 5,
        name: 'Insight',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 8,
        ability: 6,
        name: 'Intimidation',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 9,
        ability: 4,
        name: 'Investigation',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 10,
        ability: 5,
        name: 'Medicine',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 11,
        ability: 4,
        name: 'Nature',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 12,
        ability: 5,
        name: 'Perception',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 13,
        ability: 6,
        name: 'Performance',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 14,
        ability: 6,
        name: 'Persuasion',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 15,
        ability: 4,
        name: 'Religion',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 16,
        ability: 2,
        name: 'Sleight of Hand',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 17,
        ability: 2,
        name: 'Stealth',
        trainned: false,
        expertise: false,
        misc: 0
      },
      {
        id: 18,
        ability: 5,
        name: 'Survival',
        trainned: false,
        expertise: false,
        misc: 0
      }
    ]
  }),
  created() {},
  methods: {
    getSkillValue(skill) {
      let value = 0
      if (skill.expertise) {
        value += this.prof * 2
      } else if (skill.trainned) {
        value += this.prof
      }
      value += this.getAbilityModifier(skill)

      value += skill.misc

      return value
    },
    getAbilityModifier(skill) {
      for (const index in this.abilities) {
        if (this.abilities[index].id === skill.ability) {
          return this.abilities[index].mod
        }
      }
    }
  }
}
</script>

<style></style>
