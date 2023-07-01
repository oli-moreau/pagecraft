<template>
  <main>
    <NavBar :show-editor="showEditor" @toggle-editor="toggleEditor"/>
    <DraggableBlocks v-if="showEditor"/>
    <section class="main-content">
      <TransitionGroup name="row-slide">
        <template v-for="(row, rowIndex) in pageRows" :key="row.id">
          <div
              class="row"
              :class="{'row-border': showEditor}"
              :style="{'height': `${row.height}vh`}"
          >
            <div class="row-height-bar" v-if="showEditor">
              <input
                  type="text"
                  :value="row.height"
                  @keydown="numbersOnlyInput"
                  @keydown.enter.prevent="updateRowHeight(row.id, $event.target.value)"
              >
            </div>
            <TransitionGroup name="block-slide">
              <template v-for="(block, blockIndex) in row.data" :key="block.id">
                <component
                    :is="blockMap[block.type].component"
                    :blockId="block.id"
                    :showEditor="showEditor"
                    :size="block.size"
                    @remove-block="removeBlockHandler(row.id, block.id)"
                >
                </component>
                <div
                    class="block-gap"
                    :class="{'gap-being-dragged-over': isGapBeingDraggedOver[block.id]}"
                    :style="getBlockGapStyle(block)"
                    @dragover.prevent="blockGapHover(block.id, row.id)"
                    @dragleave="blockGapHoverLeave(block.id)"
                    @drop="blockDroppedInGap(rowIndex, blockIndex, $event)"
                >
                  <div class="block-gap-width-bar" v-if="showEditor">
                    <input
                        type="text"
                        :value="block.rightGap"
                        @keydown="numbersOnlyInput"
                        @keydown.enter.prevent="updateBlockRightGapWidth(block, $event.target.value)"
                    >
                  </div>
                </div>
              </template>
            </TransitionGroup>
            <DropZone
                v-if="showEditor && row.length !== 4"
                :blockId="blockId"
                :block-right-gap="defaultBlockRightGap"
                @droppedBlock="droppedInRow($event, row.id)"
            />
          </div>
          <div class="row-gap" :style="{'height': `${rowGapHeights[rowIndex]}vh`}">
            <div class="row-gap-height-bar" v-if="showEditor">
              <input
                  type="text"
                  :value="rowGapHeights[rowIndex]"
                  @keydown="numbersOnlyInput"
                  @keydown.enter.prevent="updateRowGapHeight(rowIndex, $event.target.value)"
              >
            </div>
          </div>
        </template>
      </TransitionGroup>
      <!--      v-bind="blockMap[block.type].props"-->
    </section>
    <DropZone
        style="height: 50vh"
        v-if="showEditor"
        @droppedBlock="droppedInNewRow"
    />
  </main>
</template>

<script>
import NavBar from "@/components/NavBar.vue";
import DraggableBlocks from "@/components/DraggableBlocks.vue";
import DropZone from "@/components/DropZone.vue";
import Image from "@/components/blocks/image/Image.vue";
import Text from "@/components/blocks/text/Text.vue";

export default {
  components: {
    NavBar,
    DraggableBlocks,
    DropZone,
    Image,
    Text,
  },
  data() {
    return {
      showEditor: true,
      rowId: 1,
      blockId: 1,
      defaultBlockRightGap: 5,
      pageRows: [],
      rowHeights: [],
      rowBeingDraggedOverId: null,
      rowGapHeights: [],
      isGapBeingDraggedOver: {},
      gapBeingDraggedOverId: null,
    }
  },
  mounted() {
  },
  methods: {
    toggleEditor() {
      this.showEditor = !this.showEditor
    },
    droppedInRow(droppedBlock, rowIndex) {
      droppedBlock.id = this.blockId;
      droppedBlock.rightGap = this.defaultBlockRightGap;

      const targetRow = this.pageRows.find(row => row.id === rowIndex)
      //TODO Why keep only 4?
      if (targetRow.data.length < 4) {
        targetRow.data.push(droppedBlock)
      } else {
        this.pageRows.push([droppedBlock])
      }
      this.blockId++
    },
    droppedInNewRow(droppedBlock) {
      droppedBlock.id = this.blockId;
      droppedBlock.rightGap = this.defaultBlockRightGap;

      const newRow = {
        id: this.rowId,
        height: 50,
        data: [droppedBlock]
      }

      this.pageRows.push(newRow)
      this.rowHeights.push(50)
      this.rowGapHeights.push(5)

      this.blockId++
      this.rowId++
    },
    removeBlockHandler(rowId, blockId) {
      const targetRowIndex = this.pageRows.findIndex(row => row.id === rowId)
      const targetBlockIndex = this.pageRows[targetRowIndex].data.findIndex(block => block.id == blockId)
      if (targetBlockIndex !== -1) {
        this.pageRows[targetRowIndex].data.splice(targetBlockIndex, 1)
      }
      if (this.pageRows[targetRowIndex].data.length === 0) {
        this.pageRows.splice(targetRowIndex, 1);
        this.rowHeights.splice(targetRowIndex, 1)
        this.rowGapHeights.splice(targetRowIndex, 1)
      }
    },
    updateRowHeight(rowId, value) {
      const targetRow = this.pageRows.find(row => row.id === rowId)
      targetRow.height = value
      event.target.blur()
    },
    updateRowGapHeight(rowIndex, value) {
      this.rowGapHeights[rowIndex] = value;
      event.target.blur()
    },
    updateBlockRightGapWidth(block, value) {
      block.rightGap = value
      event.target.blur()
    },
    numbersOnlyInput() {
      const keyCode = event.keyCode || event.which
      const keyValue = String.fromCharCode(keyCode)
      const validInputRegex = /^[0-9\b\t\n\r\x1B\x7F\x9A\x9D]$/

      if (!validInputRegex.test(keyValue)) {
        event.preventDefault();
      }
    },
    blockGapHover(blockId, rowId) {
      this.gapBeingDraggedOverId = blockId
      this.isGapBeingDraggedOver[blockId] = true
      this.rowBeingDraggedOverId = rowId
    },
    blockGapHoverLeave(blockId) {
      this.isGapBeingDraggedOver[blockId] = false
    },
    blockDroppedInGap(rowIndex, blockIndex, event) {
      const draggedBlockId = JSON.parse(event.dataTransfer.getData('blockBeingDraggedId'))

      const rowIndexOrigin = this.pageRows.findIndex(row => row.data.some(block => block.id === draggedBlockId))
      const blockIndexOrigin = this.pageRows[rowIndexOrigin].data.findIndex(block => block.id === draggedBlockId)

      const draggedBlock = this.pageRows[rowIndexOrigin].data[blockIndexOrigin]
      const targetBlock = this.pageRows[rowIndex].data[blockIndex]

      if (rowIndexOrigin === rowIndex) {
        this.pageRows[rowIndexOrigin].data.splice(blockIndexOrigin, 1, targetBlock);
        this.pageRows[rowIndex].data.splice(blockIndex, 1, draggedBlock);
      } else {
        this.pageRows[rowIndexOrigin].data.splice(blockIndexOrigin, 1)
        this.pageRows[rowIndex].data.splice(blockIndex+1, 0, draggedBlock)
      }

      this.isGapBeingDraggedOver[draggedBlockId] = false
      this.isGapBeingDraggedOver[targetBlock.id] = false
    },
  },
  computed: {
    blockMap() {
      return {
        'image': {
          component: Image,
          // props: (blockId, showEditor) => ({
          //   showEditor: showEditor,
          //   blockId: blockId,
          // }),
        },
        'text': {
          component: Text,
        }
      };
    },
    getBlockGapStyle() {
      return (block) => {
        return {
          width: `${block.rightGap}vw`
        }
      }
    },
  },
}
</script>
<style>
</style>