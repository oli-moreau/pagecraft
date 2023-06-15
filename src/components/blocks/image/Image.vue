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
        v-if="showEditor"
    />
    <ImageDropZone :corner-round="imageClasses.border == 'corner-round'"/>
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
        case 'drop-shadow': this.imageClasses.shadow = selectedOption; break
        case 'drop-shadow-off': this.imageClasses.shadow = ''; break
        case 'corner-round': this.imageClasses.border = selectedOption; break
        case 'corner-round-off': this.imageClasses.border = ''; break
        case 'bg-white-true' : this.imageClasses.backgroundColor = selectedOption; break
        default: this.imageClasses.size = selectedOption
      }
    },
    imageBackgroundColorHandler(event) {
      this.imageBackgroundColor = event
    },
  },
  computed: {},
}
</script>