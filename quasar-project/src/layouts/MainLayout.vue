<template>
  <q-layout view="lHh Lpr lFf" style="background-color: black;">
    <q-header elevated>
      <q-toolbar class="bg-dark">
        <q-toolbar-title class="q-pa-md">
          Anime Tracker
        </q-toolbar-title>
      </q-toolbar>
    </q-header>



    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
// import EssentialLink from 'components/EssentialLink.vue'




export default defineComponent({
  name: 'MainLayout',

  components: {

  },

  setup () {


    return {

   
    }
  }
})
</script>
