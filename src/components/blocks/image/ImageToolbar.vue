<template>
  <div class="image-toolbar corner-round-top">
    <div class="toolbar-left">
      <button class="btn-no-style btn-menu" @click="openMenu">
        <span>⋮</span>
      </button>
    </div>
    <div class="toolbar-middle">
      <span>Image</span>
    </div>
    <div class="toolbar-right">
      <button class="btn-no-style btn-remove" @click="removeBlock">
        <span>x</span>
      </button>
    </div>
    <div v-if="showMenu" class="toolbar-options drop-shadow corner-round">
      <button
          v-for="(option, index) in options"
          :key="index"
          class="toolbar-option btn-no-style"
          @click="optionSelected(option)"
      >
        {{ option.label }}
      </button>
      <div>
        <label for="background-input">Background color:</label>
        <input id="background-input" type="text" v-model="backgroundColor" />
        <button @click="imageBackgroundColor">Apply</button>
      </div>
    </div>
  </div>
</template>


<script>
export default {
  data() {
    return {
      showMenu: false,
      backgroundColor: '#ffffff',
      options: [
        { label: 'Small image', value: 'image-small' },
        { label: 'Medium image', value: 'image-medium' },
        { label: 'Large image', value: 'image-large' },
        { label: 'Full image', value: 'image-full' },
        { label: 'Drop shadow', value: 'drop-shadow'},
        { label: 'Round corner', value: 'corner-round'},
      ],
    }
  },
  mounted() {
  },
  methods: {
    removeBlock() {
      this.$emit('removeBlock');
    },
    openMenu() {
      this.showMenu = !this.showMenu
      this.$emit('editBlock')
    },
    optionSelected(selectedOption) {
      this.$emit('optionSelected', selectedOption.value)
      this.showMenu = false
    },
    imageBackgroundColor() {
      if (this.backgroundColor) {
        this.$emit('imageBackgroundColor', this.backgroundColor)
      }
      this.showMenu = false;
    },
  },
  computed: {},
}
</script>