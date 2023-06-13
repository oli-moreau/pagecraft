<template>
  <div class="image-dropzone"
       :class="{
            'image-dropped': imageDropped,
            'is-image-hovering': isImageHovering,
         }"
       @drop="handleDrop"
       @dragover="handleDragOver"
       @dragleave="isImageHovering = false"
  >
    <span v-if="!file">Upload image</span>
    <img class="image-preview" v-else :src="file" alt="Dropped Image">
  </div>
</template>

<script>
export default {
  data() {
    return {
      file: null,
      imageDropped: false,
      isImageHovering: false,
      fileTypes: ['image/png', 'image/jpeg', 'image/svg+xml', 'image/gif']
    }
  },
  methods: {
    handleDrop(event) {
      event.preventDefault()
      const file = event.dataTransfer.files[0]

      if (file == undefined || !this.fileTypes.includes(file.type)) {
        return alert('Invalid file type. Please upload a PNG, JPG, or SVG file.')
      }

      const reader = new FileReader()

      reader.onload = () => {
        this.file = reader.result
        this.imageDropped = true
      };

      reader.readAsDataURL(file)
    },
    handleDragOver(event) {
      this.isImageHovering = true
      event.preventDefault()
    }
  }
};
</script>