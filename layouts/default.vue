<template>
  <v-app>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer />
      <v-menu offset-y>
        <template #activator="{ on, attrs }">
          <v-btn
            elevation="2"
            icon
            v-bind="attrs"
            v-on="on"
          >
            <v-icon>
              mdi-translate
            </v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item>
            <v-btn elevation="0">
              <nuxt-link :to="switchLocalePath('ru')">
                Русский
              </nuxt-link>
            </v-btn>
          </v-list-item>
          <v-list-item>
            <v-btn elevation="0">
              <nuxt-link :to="switchLocalePath('en')">
                English
              </nuxt-link>
            </v-btn>
          </v-list-item>
        </v-list>
      </v-menu>
      <v-spacer style="max-width: 100px" />
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }} Istigechev Boris</span>
    </v-footer>
  </v-app>
</template>

<style>
.v-btn__content a {
  text-decoration: none;
}
</style>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      clipped: false,
      fixed: false,
      title: 'CurrencyConverter'
    }
  }
}
</script>
