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
              <template v-for="block in row.data" :key="block.id">
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
                    :style="getBlockGapStyle(block.id)"
                    @dragover.prevent="blockGapHover(block.id)"
                    @dragleave="blockGapHoverLeave(block.id)"
                    @drop="blockDroppedInGap(rowIndex, $event)"
                >
                  <div class="block-gap-width-bar" v-if="showEditor">
                    <input
                        type="text"
                        :value="blockGapWidth[block.id]"
                        @keydown="numbersOnlyInput"
                        @keydown.enter.prevent="updateBlockGapWidth(block.id, $event.target.value)"
                    >
                  </div>
                </div>
              </template>
            </TransitionGroup>
            <DropZone
                v-if="showEditor && row.length !== 4"
                :blockId="blockId"
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
        :blockId="blockId"
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
      rowId: 0,
      blockId: 1,
      pageRows: [],
      rowHeights: [],
      rowGapHeights: [],
      blockGapWidth: {},
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
      // const targetRow = this.pageRows[rowIndex];
      const targetRow = this.pageRows.find(row => row.id === rowIndex)
      this.blockId++
      if (targetRow.data.length < 4) {
        targetRow.data.push(droppedBlock)
        this.blockGapWidth[droppedBlock.id] = 5
      } else {
        console.log('keep after 4?')
        // this.pageRows.push([droppedBlock])
      }
    },
    droppedInNewRow(droppedBlock) {
      this.blockId++
      this.rowId++
      const newRow = {
        id: this.rowId,
        height: 50,
        data: [droppedBlock]
      }
      this.pageRows.push(newRow)
      // this.pageRows.push([droppedBlock])
      this.rowHeights.push(50)
      this.rowGapHeights.push(5)
      this.blockGapWidth[droppedBlock.id] = 5
    },
    removeBlockHandler(rowId, blockId) {
      const targetRowIndex = this.pageRows.findIndex(row => row.id === rowId)
      const targetBlockIndex = this.pageRows[targetRowIndex].data.findIndex(block => block.id == blockId)
      if (targetBlockIndex !== -1) {
        this.pageRows[targetRowIndex].data.splice(targetBlockIndex, 1)
        delete this.blockGapWidth[blockId]
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
    updateBlockGapWidth(blockId, value) {
      this.blockGapWidth[blockId] = value;
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
    blockGapHover(blockId) {
      this.gapBeingDraggedOverId = blockId
      this.isGapBeingDraggedOver[blockId] = true
    },
    blockGapHoverLeave(blockId) {
      this.isGapBeingDraggedOver[blockId] = false
    },
    //TODO might re-think the whole block-gap concept
    //TODO Should be its own thing instead of being bound to a block
    blockDroppedInGap(rowIndex, event) {
      console.log('todo')
      // const movedBlockId = JSON.parse(event.dataTransfer.getData('blockBeingMovedId'))
      // const currentBlockIndex = this.pageRows[rowIndex].data.findIndex(block => block.id === movedBlockId)
      //
      // const targetPageRowsIndex = this.pageRows.findIndex(row => row.some(block => block.id === this.gapBeingDraggedOverId))
      // const targetBlockIndex = this.pageRows[targetPageRowsIndex].findIndex(block => block.id === this.gapBeingDraggedOverId)
      // console.log(targetPageRowsIndex)


      // const currentBlockIndex = this.pageRows[currentPageRowsIndex].findIndex(block => block.id === movedBlockId)
      //
      // const targetPageRowsIndex = this.pageRows.findIndex(row => row.some(block => block.id === this.gapBeingDraggedOverId))
      // const targetBlockIndex = this.pageRows[targetPageRowsIndex].findIndex(block => block.id === this.gapBeingDraggedOverId)
      //
      // const currentBlock = this.pageRows[currentPageRowsIndex][currentBlockIndex]
      // const targetBlock = this.pageRows[targetPageRowsIndex][targetBlockIndex]
      //
      // if (currentPageRowsIndex === targetPageRowsIndex) {
      //   this.pageRows[currentPageRowsIndex].splice(currentBlockIndex, 1, targetBlock);
      //   this.pageRows[targetPageRowsIndex].splice(targetBlockIndex, 1, currentBlock);
      // } else {
      //   this.pageRows[currentPageRowsIndex].splice(currentBlockIndex, 1)
      //   this.pageRows[targetPageRowsIndex].splice(targetBlockIndex+1, 0, currentBlock)
      // }
      //
      // this.isGapBeingDraggedOver[movedBlockId] = false
      // this.isGapBeingDraggedOver[targetBlock.id] = false
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
      return (blockId) => {
        return {
          width: `${this.blockGapWidth[blockId]}vw`
        }
      }
    },
  },
}
</script>
<style>
</style>