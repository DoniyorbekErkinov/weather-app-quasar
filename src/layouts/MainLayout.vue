<template>
  <q-layout view="lHh Lpr lFf">
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
export default {
  name: 'MainLayout',
  components: {
  },
  data() {
    return {
    }
  }
}
</script>
<style lang="sass">
.q-page-container
  background: linear-gradient(to right, #26D0CE, #1A2980) 
</style>
