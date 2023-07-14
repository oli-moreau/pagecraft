<template>
  <div class="image-toolbar corner-round-top">
    <div class="toolbar-left">
      <button class="btn-no-style btn-menu" @click="openMenu">
        <span>⋮</span>
      </button>
    </div>
    <div class="toolbar-middle">
      <span>Text</span>
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
    </div>
  </div>
</template>

<script>
export default {
  props: {
    textBackgroundColor: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      backgroundColor : null,
      showMenu: false,
      options: [
        { label: 'Small text', value: 'text-small' },
        { label: 'Medium text', value: 'text-medium' },
        { label: 'Large text', value: 'text-large' },
        { label: 'Full text', value: 'text-full' },
        { label: 'Drop shadow', value: 'drop-shadow'},
        { label: 'Round corner', value: 'corner-round'},
      ],
    }
  },
  mounted() {
    this.backgroundColor = this.textBackgroundColor
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
  },
  computed: {},
}
</script>