<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="glossy dark shadow-2">
          <q-avatar>
            <q-icon name="place" style="color:black" size="2em" />
          </q-avatar>

          <q-toolbar-title>Find The Nearest Pharmacy</q-toolbar-title>
        </q-toolbar>
    </q-header>

   

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'

export default defineComponent({
  name: 'MainLayout',
  setup () {
    const leftDrawerOpen = ref(false)

    return {
   
     
    }
  }
})
</script>
