<template>
  <div :class="Object.values(textClasses)"
       :style="{
          'background-color': textClasses.backgroundColor,
        }"
       :draggable="isToolbarHovered"
       @dragstart="event => blockTextDragStart(event, blockId)"
       @dragend="blockImageDragStop"
  >
    <TextToolbar
        :text-background-color="textClasses.backgroundColor"
        @mouseover="isToolbarHovered = true"
        @mouseleave="isToolbarHovered = false"
        @edit-block="onEditBlock"
        @remove-block="onRemoveBlock"
        @option-selected="onOptionSelected"
        @background-color="textBackgroundColorHandler"
        v-if="showEditor"
    />
    <div class="tinymce-wrapper" v-if="showEditor">
      <Tinymce
          :block-content="blockContent"
          :block-id="blockId"
          @update-block-content="updateBlockContent"
      />
    </div>
    <div class="block-content" v-show="!showEditor">
      <span v-html="blockContent"></span>
    </div>
  </div>
</template>


<script>
import TextToolbar from "@/components/blocks/text/TextToolbar.vue";
import ImageDropZone from "@/components/blocks/ImageDropZone.vue";
import Tinymce from "@/components/Tinymce.vue";
import Toolbar from "@/components/blocks/image/ImageToolbar.vue";
export default {
  components: {Toolbar, Tinymce, TextToolbar, ImageDropZone},
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
      textClasses: {
        size: "",
        border: "corner-round",
        shadow: "drop-shadow",
        backgroundColor: '#ffffff'
      },
      blockContent: '',
      editBlockContent: false,
      insertImage: false,
      isToolbarHovered: false,
      isBlockTextDragged: false,
    }
  },
  mounted() {
    this.textClasses.size = this.size
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
        case 'drop-shadow': this.modifyTextClass('shadow', selectedOption); break
        case 'corner-round': this.modifyTextClass('border', selectedOption); break
        default: this.textClasses.size = selectedOption
      }
    },
    modifyTextClass(key, value) {
      this.textClasses[key] === ''
          ? this.textClasses[key]= value
          : this.textClasses[key] = ''
    },
    updateBlockContent(newValue) {
      this.blockContent = newValue
    },
    textBackgroundColorHandler(event) {
      event[0] === '#'
          ? this.textClasses.backgroundColor = event
          : this.textClasses.backgroundColor = '#' + event
    },
    blockTextDragStart(ev, blockTextId) {
      this.isBlockTextDragged = true
      ev.dataTransfer.setData('blockBeingDraggedId', JSON.stringify(blockTextId))
    },
    blockImageDragStop() {
      this.isBlockTextDragged = false
    },
  },
  computed: {},
}
</script>