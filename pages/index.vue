<template>
  <v-row justify="center" align="center" class="mt-10">
    <v-col justify="center" align="center">
      <h1> 修正前 </h1>
      <sign-canvas />
      <sign-canvas :scale="1.2" />
    </v-col>
    <v-col justify="center" align="center">
      <h1 class="mt-10"> 修正後 </h1>
      <sign-canvas :is-after="true" />
      <sign-canvas :scale="1.2" :is-after="true" />
    </v-col>
  </v-row>
</template>
<script>
import SignCanvas from '../components/SignCanvas'
export default {
  components: { SignCanvas },
}
</script>
<style lang="scss">
</style>
