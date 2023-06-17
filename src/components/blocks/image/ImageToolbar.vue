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
      <input class="background-color-input" type="text" v-model="backgroundColor" @input="backgroundColorHandler"/>
      <input class="image-opacity-slider" type="range" min="0" max="100" v-model="opacity" @input="opacityHandler">
    </div>
  </div>
</template>

<script>
export default {
  props: {
    imageBackgroundColor: {
      type: String,
      required: true
    },
    imageOpacity: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      backgroundColor : null,
      opacity: null,
      showMenu: false,
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
    this.backgroundColor = this.imageBackgroundColor
    this.opacity = this.imageOpacity
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
    backgroundColorHandler() {
        this.$emit('backgroundColor', this.backgroundColor)
    },
    opacityHandler() {
      this.$emit('opacity', this.opacity)
    },
  },
  computed: {},
}
</script>