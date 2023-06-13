<template>
  <main>
    <NavBar :show-editor="showEditor" @toggle-editor="toggleEditor"/>
    <DraggableBlocks v-if="showEditor"/>
    <section class="main-content">
      <div
          class="row"
          :class="{'row-border': showEditor}"
          v-for="(row, rowIndex) in pageRows"
          :key="rowIndex"
      >
        <component
            v-for="block in row"
            :key="block.id"
            :is="blockMap[block.type].component"
            :blockId="blockId"
            :showEditor="showEditor"
            :size="block.size"
            @remove-block="removeBlockHandler(rowIndex, block.id)"
        ></component>
        <DropZone
            v-if="showEditor && row.length !== 4"
            :blockId="blockId"
            @droppedBlock="droppedInRow($event, rowIndex)"
        />
      </div>
<!--      v-bind="blockMap[block.type].props"-->
    </section>
    <DropZone
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
      pageRows: [],
      blockId: 1,
    }
  },
  mounted() {
  },
  methods: {
    toggleEditor() {
      console.log(this.pageRows)
      this.showEditor = !this.showEditor
    },
    droppedInRow(droppedBlock, rowIndex) {
      const targetRow = this.pageRows[rowIndex];
      this.blockId += 1
      if (targetRow.length < 4) {
        targetRow.push(droppedBlock)
      } else {
        this.pageRows.push([droppedBlock])
      }
    },
    droppedInNewRow(droppedBlock) {
      this.blockId += 1
      this.pageRows.push([droppedBlock])
    },
    removeBlockHandler(row, elementId) {
      const index = this.pageRows[row].findIndex(el => el.id == elementId)
      if (index !== -1) {
        this.pageRows[row].splice(index, 1);
      }
      if (this.pageRows[row].length === 0) {
        this.pageRows.splice(row, 1);
      }
    },
    onRemoveRow() {
      this.$emit('removeRow');
    },
    onEditRow() {
      this.$emit('editRow');
    },
    onOptionSelected(selectedOption) {
      // this.imageClasses.size = selectedOption
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
  },
}
</script>