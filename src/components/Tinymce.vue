<template>
  <textarea :ref="'mytextarea-' + blockId"></textarea>
</template>

<script>
import tinymce from "tinymce";
import "tinymce/themes/silver/theme"
import "tinymce/models/dom/model"
import "tinymce/icons/default/icons"
import "tinymce/skins/ui/oxide/skin.min.css"
import "tinymce/skins/ui/oxide/content.min.css"
import "tinymce/plugins/lists/plugin"
import "tinymce/plugins/table/plugin"
export default {
  props: {
    blockContent: {
      type: String,
      required: true
    },
    blockId: {
      type: Number,
      required: true,
    }
  },
  mounted() {
    tinymce.init({
      skin: false,
      content_css: false,
      height: '100%',
      width: '100%',
      target: this.$refs['mytextarea-' + this.blockId],
      branding: false,
      menubar: false,
      plugins: "lists table",
      toolbar: "fontfamily fontsize | bold italic | aligncenter alignleft alignright alignjustify | bullist numlist | table",
      statusbar: false,
      setup: (editor) => {
        editor.on('init', () => {
          editor.setContent(this.blockContent);
        });
        editor.on('change keypress', () => {
          this.$emit('updateBlockContent', editor.getContent());
        });
      }
    });
  },
}
</script>

<style>

</style>

<style scoped>
@import "tinymce/skins/content/default/content.css";
</style>