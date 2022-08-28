<template>
  <div>
    <div class="grid grid-cols-12 ">
      <calendar  class="col-start-6 col-span-2"/>
    </div>
    <div class="grid grid-cols-12 ">
      <full-calendar class="col-start-4 col-span-6"/>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'IndexPage'
})
</script>
