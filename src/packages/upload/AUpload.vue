<template>
  <div
    ref="upload"
    class="a-upload"
    @dragenter="handleDragEnter"
    @dragover="handleDragOver"
    @dragleave="handleDragLeave"
    @drop="handleDrop"
  >
    <slot v-if="showDefault"></slot>
    <slot v-if="dragging" name="dragging"></slot>
    <slot v-if="compStatus === 'uploading'" name="uploading"></slot>
    <slot v-if="compStatus === 'error'" name="error"></slot>
    <slot v-if="compStatus === 'success'" name="success"></slot>
  </div>
</template>

<script lang="ts">
// This component itself doesn't trigger or handle any upload operation.
// It's just a template and supports exposing dropped files to its parent.
import { defineComponent, PropType } from 'vue';
import type { UploadStatus } from './types';

export default defineComponent({
  name: 'AUpload',
  props: {
    status: {
      type: String as PropType<UploadStatus>,
      default: '',
    },
  },
  emits: ['upload'],
  data() {
    return {
      dragging: false,
      compStatus: 'default',
    };
  },
  computed: {
    showDefault() {
      return (this.compStatus === 'default' || !this.compStatus) && !this.dragging;
    },
  },
  watch: {
    status(val) {
      this.compStatus = val;
    },
  },
  methods: {
    handleDragEnter(e: DragEvent) {
      this.dragging = true;
      e.preventDefault();
    },
    handleDragOver(e: DragEvent) {
      e.preventDefault();
    },
    handleDragLeave(e: DragEvent) {
      const currentTarget = e.currentTarget as HTMLElement;
      if (currentTarget?.contains(e.relatedTarget as HTMLElement)) {
        return;
      }
      this.dragging = false;
    },
    handleDrop(e: DragEvent) {
      if (!e.dataTransfer) {
        return;
      }
      const files = e.dataTransfer.files;
      if (files?.length) {
        this.$emit('upload', files[0]);
      }
      this.dragging = false;
      e.preventDefault();
      e.stopPropagation();
    },
  },
});
</script>


<style lang="scss">
.a-upload {
  width: 100%;
  height: 100%;
  border-radius: 24px;
  filter: drop-shadow(2px 2px 4px var(--shadow-10));
  border: 2px dashed vaR(--border);
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
