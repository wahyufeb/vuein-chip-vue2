<script lang="ts">
import Vue from 'vue';
// Uncomment import and local "components" registration if library is not registered globally.
// import { VueinChipsSample } from '@/entry.esm';

export default Vue.extend({
  name: 'ServeDev',
  // components: {
  //  VueinChipsSample,
  // }
  data: () => ({
    value: [],
    label: "test",
    errorMessage: "test",
    regex: /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/
  }),
  methods: {
    /**
     * Function to validate chips
     * Return true if chips is valid
     * Return false if chips is invalid
     * @param value
     */
    validation(value: string) {
      return isNaN(Number(value))
    }
  }
});
</script>

<template>
  <div id="app">
    <vuein-chips-sample v-model="value" />
  </div>
</template>
