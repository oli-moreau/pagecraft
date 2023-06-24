<template>
  <main>
    <NavBar :show-editor="showEditor" @toggle-editor="toggleEditor"/>
    <DraggableBlocks v-if="showEditor"/>
    <section class="main-content">
      <template v-for="(row, rowIndex) in pageRows" :key="rowIndex">
        <div
            class="row"
            :class="{'row-border': showEditor}"
            :style="{'height': `${rowHeights[rowIndex]}vh`}"
        >
          <div class="row-height-bar" v-if="showEditor">
            <input
                type="text"
                :value="rowHeights[rowIndex]"
                @keydown="numbersOnlyInput"
                @keydown.enter.prevent="updateRowHeight(rowIndex, $event.target.value)"
            >
          </div>
          <template v-for="block in row" :key="block.id">
              <component
                  :is="blockMap[block.type].component"
                  :blockId="block.id"
                  :showEditor="showEditor"
                  :size="block.size"
                  @image-being-dragged-id="imageBeingDraggedIdHandler"
                  @remove-block="removeBlockHandler(rowIndex, block.id)"
              >
              </component>
              <div class="component-gap"
                   :class="{'gap-being-dragged-over': isGapBeingDraggedOver[block.id]}"
                   :style="getComponentGapStyle(block.id)"
                    @dragover.prevent="componentGapHover(block.id)"
                    @dragleave="componentGapHoverLeave(block.id)"
                    @drop="testFn">
                <div class="component-gap-width-bar" v-if="showEditor">
                  <input
                      type="text"
                      :value="componentGapWidth[block.id]"
                      @keydown="numbersOnlyInput"
                      @keydown.enter.prevent="updateComponentGapWidth(block.id, $event.target.value)"
                  >
                </div>
              </div>
          </template>
          <DropZone
              :style="{
              'height': `${rowHeights[rowIndex]}vh`,
            }"
              v-if="showEditor && row.length !== 4"
              :blockId="blockId"
              @droppedBlock="droppedInRow($event, rowIndex)"
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
      blockId: 1,
      pageRows: [],
      rowHeights: [],
      rowGapHeights: [],
      componentGapWidth: {},
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
      const targetRow = this.pageRows[rowIndex];
      this.blockId += 1
      if (targetRow.length < 4) {
        targetRow.push(droppedBlock)
        this.componentGapWidth[droppedBlock.id] = 5
      } else {
        this.pageRows.push([droppedBlock])
      }
    },
    droppedInNewRow(droppedBlock) {
      this.blockId += 1
      this.pageRows.push([droppedBlock])
      this.rowHeights.push(50)
      this.rowGapHeights.push(5)
      this.componentGapWidth[droppedBlock.id] = 5
    },
    removeBlockHandler(row, blockId) {
      const index = this.pageRows[row].findIndex(block => block.id == blockId)
      if (index !== -1) {
        this.pageRows[row].splice(index, 1);
        delete this.componentGapWidth[blockId]
      }
      if (this.pageRows[row].length === 0) {
        this.pageRows.splice(row, 1);
        this.rowHeights.splice(row, 1)
        this.rowGapHeights.splice(row, 1)
      }
    },
    updateRowHeight(rowIndex, value) {
      this.rowHeights[rowIndex] = value;
      event.target.blur()
    },
    updateRowGapHeight(rowIndex, value) {
      this.rowGapHeights[rowIndex] = value;
      event.target.blur()
    },
    updateComponentGapWidth(blockId, value) {
      this.componentGapWidth[blockId] = value;
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
    componentGapHover(blockId) {
      this.gapBeingDraggedOverId = blockId
      this.isGapBeingDraggedOver[blockId] = true
    },
    componentGapHoverLeave(blockId) {
      this.isGapBeingDraggedOver[blockId] = false
    },
    imageBeingDraggedIdHandler(blockId) {
      // console.log(this.pageRows)
      // console.log(blockId)
    },
    testFn(ev) {
      const movedBlockId = JSON.parse(ev.dataTransfer.getData('blockImageId'))

      const currentPageRowsIndex = this.pageRows.findIndex(row => row.some(block => block.id === movedBlockId))
      const currentBlockIndex = this.pageRows[currentPageRowsIndex].findIndex(block => block.id === movedBlockId)

      const targetPageRowsIndex = this.pageRows.findIndex(row => row.some(block => block.id === this.gapBeingDraggedOverId))
      const targetBlockIndex = this.pageRows[targetPageRowsIndex].findIndex(block => block.id === this.gapBeingDraggedOverId)

      const currentBlock = this.pageRows[currentPageRowsIndex][currentBlockIndex]
      const targetBlock = this.pageRows[targetPageRowsIndex][targetBlockIndex]

      if (currentPageRowsIndex === targetPageRowsIndex) {
        this.pageRows[currentPageRowsIndex].splice(currentBlockIndex, 1, targetBlock);
        this.pageRows[targetPageRowsIndex].splice(targetBlockIndex, 1, currentBlock);
      } else {
        this.pageRows[currentPageRowsIndex].splice(currentBlockIndex, 1)
        this.pageRows[targetPageRowsIndex].splice(targetBlockIndex+1, 0, currentBlock)
      }

      this.isGapBeingDraggedOver[movedBlockId] = false
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
    getComponentGapStyle() {
      return (blockId) => {
        return {
          width: `${this.componentGapWidth[blockId]}vw`
        }
      }
    },
  },
}
</script>