<template>
  <section class="topnav">
    <div class="logo-wrapper">
      <img class="logo link-black" src="../../Icons/hammer.png" alt="nav-logo" @click="toggleEditor">
    </div>
    <div class="nav-links">
      <span class="text-lg link-black">Item 1</span>
      <span class="text-lg link-black">Item 2</span>
      <span class="text-lg link-black">Item 3</span>
      <span class="text-lg link-black">Item 4</span>
    </div>
  </section>
</template>

<script>
export default {
  props: ['show-editor'],
  data() {
    return {}
  },
  methods: {
    toggleEditor() {
      this.$emit('toggle-editor');
    }
  },
}
</script>