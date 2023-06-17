<template>
  <div
      :class="Object.values(imageClasses)"
      :style="{
          'background-color': imageBackgroundColor,
        }"
  >
    <Toolbar
        @edit-block="onEditBlock"
        @remove-block="onRemoveBlock"
        @option-selected="onOptionSelected"
        @image-background-color="imageBackgroundColorHandler"
        @image-opacity="imageOpacityHandler"
        v-if="showEditor"
    />
    <ImageDropZone
        :style="{
          'opacity' : imageOpacity,
        }"
        :corner-round="imageClasses.border === 'corner-round'"
    />
  </div>
</template>


<script>
import Toolbar from "@/components/blocks/image/ImageToolbar.vue";
import ImageDropZone from "@/components/blocks/ImageDropZone.vue";
export default {
  components: {Toolbar, ImageDropZone},
  props: {
    showEditor: {
      type: Boolean,
      required: true,
    },
    size: {
      type: String,
      required: true
    },
    blockId: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      blockContent: '',
      editBlockContent: false,
      insertImage: false,
      imageBackgroundColor: 'white',
      imageOpacity: 1,
      imageClasses: {
        size: "",
        border: "corner-round",
        shadow: "drop-shadow",
      },
    }
  },
  mounted() {
    this.imageClasses.size = this.size
  },
  methods: {
    onRemoveBlock() {
      this.$emit('removeBlock');
    },
    onEditBlock() {
      this.$emit('editBlock');
    },
    onOptionSelected(selectedOption) {
      switch (selectedOption) {
        case 'drop-shadow': this.modifyImageClass('shadow', selectedOption); break
        case 'corner-round': this.modifyImageClass('border', selectedOption); break
        default: this.imageClasses.size = selectedOption
      }
    },
    modifyImageClass(key, value) {
      this.imageClasses[key] === ''
          ? this.imageClasses[key]= value
          : this.imageClasses[key] = ''
    },
    imageBackgroundColorHandler(event) {
      this.imageBackgroundColor = event
    },
    imageOpacityHandler(event) {
      this.imageOpacity = (event / 100)
    },
  },
  computed: {},
}
</script>