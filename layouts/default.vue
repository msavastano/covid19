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
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer :fixed="fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: true,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        // {
        //   icon: 'mdi-octagram',
        //   title: 'Global Covid',
        //   to: '/global-covid'
        // },
        // {
        //   icon: 'mdi-chart-areaspline',
        //   title: 'Global Chart',
        //   to: '/global-chart'
        // },
        // {
        //   icon: 'mdi-star',
        //   title: 'US Covid',
        //   to: '/us-covid'
        // },
        {
          icon: 'mdi-chart-line-variant',
          title: 'US Chart',
          to: '/us-chart'
        },
        {
          icon: 'mdi-check-all',
          title: 'US Testing Chart',
          to: '/us-testing'
        }
      ],
      miniVariant: false,
      title: 'Covid-19'
    }
  }
}
</script>

<style>
.select-rows {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  align-items: center;
}
</style>
