<template>
  <div :class="Object.values(textClasses)">
    <Toolbar
        @edit-block="onEditBlock"
        @remove-block="onRemoveBlock"
        @option-selected="onOptionSelected"
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
import Toolbar from "@/components/blocks/image/ImageToolbar.vue";
import ImageDropZone from "@/components/blocks/ImageDropZone.vue";
import Tinymce from "@/components/Tinymce.vue";
export default {
  components: {Tinymce, Toolbar, ImageDropZone},
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
      },
      blockContent: '',
      editBlockContent: false,
      insertImage: false,
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
        this.textClasses.size = selectedOption
    },
    updateBlockContent(newValue) {
      this.blockContent = newValue
    }
  },
  computed: {},
}
</script>