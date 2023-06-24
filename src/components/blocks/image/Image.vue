<template>
  <div
      :class="Object.values(imageClasses)"
      :style="{
          'background-color': imageBackgroundColor,
        }"
  >
<!--    draggable="true"-->
<!--    @dragstart="blockImageDragStart"-->
<!--    @dragend="blockImageDragStop"-->
    <Toolbar
        :image-background-color="imageBackgroundColor"
        :image-opacity="imageOpacity"
        @edit-block="onEditBlock"
        @remove-block="onRemoveBlock"
        @option-selected="onOptionSelected"
        @background-color="imageBackgroundColorHandler"
        @opacity="imageOpacityHandler"
        v-if="showEditor"
    />
    <ImageDropZone
        :style="{
          'opacity' : imageOpacityCalculated,
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
      isBlockImageDragged: false,
      insertImage: false,
      imageBackgroundColor: '#ffffff',
      imageOpacity: 100,
      imageOpacityCalculated: null,
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
      event[0] === '#'
        ? this.imageBackgroundColor = event
        : this.imageBackgroundColor = '#' + event
    },
    imageOpacityHandler(event) {
      this.imageOpacity = Number(event)
      this.imageOpacityCalculated = (this.imageOpacity / 100)
    },
    blockImageDragStart(event) {
      console.log(this.blockId)
      this.isBlockImageDragged = true
      // console.log(event.dataTransfer(''))
    },
    blockImageDragStop(event) {
      this.isBlockImageDragged = false
      console.log(event)
    }
  },
  computed: {},
}
</script>