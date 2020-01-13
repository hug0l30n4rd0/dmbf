<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md6>
      <div class="text-center">
        <v-img src="xampu2.jpg" height="80%" />
      </div>
      <v-card>
        <v-card-title class="headline">
          Welcome to the Dungeon Master's Best Friend
        </v-card-title>
        <v-card-text>
          <p>
            We shall be your guide.
          </p>
          <p>
            Join our +3 protection aura and follow us through this magnifecent
            journey.
          </p>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" nuxt to="/spell">
            Continue
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
// import Logo from '~/components/Logo.vue'
// import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
  // components: {
  //   Logo,
  //   VuetifyLogo
  // }
}
</script>
