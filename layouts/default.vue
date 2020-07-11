<template>
  <v-app dark>
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

    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      items: [
        {
          icon: 'mdi-chart-areaspline',
          title: 'US Confirmed Cases',
          to: '/us-cases'
        },
        {
          icon: 'mdi-check-all',
          title: 'US Testing Rates',
          to: '/us-testing'
        },
        {
          icon: 'mdi-chart-line-stacked',
          title: 'US Deaths',
          to: '/us-deaths'
        },
        {
          icon: 'mdi-alpha-u-circle-outline',
          title: 'All US',
          to: '/us-all-states'
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
