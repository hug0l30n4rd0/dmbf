<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />

      <v-toolbar-title v-text="title" />
      <v-spacer />

      <NavbarHeader />
    </v-app-bar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-navigation-drawer v-model="rightDrawer" :right="right" temporary fixed>
      <v-list>
        <v-list-item @click.native="right = !right">
          <v-list-item-action>
            <v-icon light>
              mdi-repeat
            </v-icon>
          </v-list-item-action>
          <v-list-item-title>Switch drawer (click me)</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-footer :fixed="fixed" app>
      <span
        >&copy; 2019 - Logged: {{ this.$auth.loggedIn }} ~ {{ this.$auth.user }}
      </span>
    </v-footer>
  </v-app>
</template>

<script>
import NavbarHeader from '@/components/NavbarHeader'

export default {
  components: {
    NavbarHeader
  },
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        // {
        //   icon: 'ra ra-lightning-sword',
        //   title: 'Magic Items',
        //   to: '/items'
        // },
        {
          icon: 'ra ra-lightning-trio',
          title: 'Spells',
          to: '/spell'
        },
        {
          icon: 'ra ra-fighter',
          title: 'Character',
          to: '/character'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "The Dungeon Master's Best Friend"
    }
  }
}
</script>
