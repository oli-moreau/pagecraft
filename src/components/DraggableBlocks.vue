<template>
  <div class="draggable-blocks-main drop-shadow">
    <div v-for="block in blocks" :key="block.id"
         class="draggable-block corner-round drop-shadow"
         draggable="true"
         @dragstart="event => drag(event, block.type, block.size)">
      <span>{{ block.name }}</span>
    </div>
  </div>

</template>

<script>
export default {
  data() {
    return {
      blocks: {
        'image-small' : {
          name: 'Small image',
          type: 'image',
          size: 'image-small'
        },
        'image-medium' : {
          name: 'Medium image',
          type: 'image',
          size: 'image-medium'
        },
        'image-large' : {
          name: 'Large image',
          type: 'image',
          size: 'image-large'
        },
        'image-full' : {
          name: 'Full width image',
          type: 'image',
          size: 'image-full'
        },
        'text-small' : {
          name: 'Small text',
          type: 'text',
          size: 'text-small'
        },
        'text-medium' : {
          name: 'Medium text',
          type: 'text',
          size: 'text-medium'
        },
        'text-large' : {
          name: 'Large text',
          type: 'text',
          size: 'text-large'
        },
        'text-full' : {
          name: 'Full text',
          type: 'text',
          size: 'text-full'
        },
      }
    }
  },
  methods: {
    drag(ev, type, size) {
      const data = {
        type: type,
        size: size
      };
      ev.dataTransfer.setData('block', JSON.stringify(data));
    }
  }
}
</script>