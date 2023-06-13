<template>
  <div class="builder-drop-zone"
       :class="{'block-hovering': isBlockHovering}"
       @drop="drop"
       @dragover.prevent="blockHovering"
       @dragleave="isBlockHovering = false"
  >
<!--    @dragenter.prevent="allowDrop(ev)"-->
    <span>Drop component here</span>
  </div>
</template>

<script>
export default {
  props: {
    blockId: {
      type: Number,
      required: true,
    }
  },
  data() {
    return {
      isBlockHovering: false,
    }
  },
  methods: {
    drop(ev) {
      this.isBlockHovering = false
      const droppedBlock = JSON.parse(ev.dataTransfer.getData('block'))
      droppedBlock.id = this.blockId
      this.$emit('droppedBlock', droppedBlock)
    },
    blockHovering() {
      this.isBlockHovering = true
    },
  }
}
</script>