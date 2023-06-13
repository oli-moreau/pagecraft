<template>
  <section class="topnav">
    <div class="logo-wrapper">
      <img class="logo link-black" src="../../public/images/Icons/skin-care.png" alt="nav-logo" @click="toggleEditor">
    </div>
    <div class="nav-links">
      <span class="text-lg link-black">Creams</span>
      <span class="text-lg link-black">Face masks</span>
      <span class="text-lg link-black">Serums</span>
      <span class="text-lg link-black">All products</span>
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