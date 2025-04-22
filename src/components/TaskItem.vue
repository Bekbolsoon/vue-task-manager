<template>
  <div class="task-item">
    <p class="task-item-text">{{ task.text }}</p>
    <button
      @click="showContextMenu(task.id, sectionId, $event)"
      class="task-item__menu-btn"
      :ref="`menuButton-${task.id}`">
      <svg width="21" height="20" viewBox="0 0 21 20" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M10.2 10.8333C10.6603 10.8333 11.0334 10.4602 11.0334 10C11.0334 9.53977 10.6603 9.16667 10.2 9.16667C9.73978 9.16667 9.36669 9.53977 9.36669 10C9.36669 10.4602 9.73978 10.8333 10.2 10.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
        <path d="M16.0334 10.8333C16.4936 10.8333 16.8667 10.4602 16.8667 10C16.8667 9.53977 16.4936 9.16667 16.0334 9.16667C15.5731 9.16667 15.2 9.53977 15.2 10C15.2 10.4602 15.5731 10.8333 16.0334 10.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
        <path d="M4.36669 10.8333C4.82693 10.8333 5.20002 10.4602 5.20002 10C5.20002 9.53977 4.82693 9.16667 4.36669 9.16667C3.90645 9.16667 3.53336 9.53977 3.53336 10C3.53336 10.4602 3.90645 10.8333 4.36669 10.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
      </svg>
    </button>

    <ContextMenu
      v-if="activeContextMenu && activeContextMenu.taskId === task.id && activeContextMenu.sectionId === sectionId"
      :taskId="task.id"
      @delete-task="deleteTask"
    />
  </div>
</template>

<script>
import ContextMenu from "./ContextMenu.vue";

export default {
  components: { ContextMenu },
  props: {
    task: {
      type: Object,
      required: true,
    },
    sectionId: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      activeContextMenu: null,
    };
  },
  methods: {
    showContextMenu(taskId, sectionId, event) {
      this.activeContextMenu = { taskId, sectionId };
      this.$emit("show-context-menu", taskId, sectionId, event);
    },
    deleteTask(taskId, sectionId) {
      this.$emit("delete-task", taskId, sectionId);
    },
  },
};
</script>

<style scoped>
.task-item {
  border: 1px solid #c4cad4;
  border-radius: 4px;
  padding: 8px 24px 8px 8px;
  background: #fff;
  position: relative;
  margin-top: 8px;
}

.task-item-text {
  font-weight: 400;
  font-size: 14px;
  line-height: 129%;
  color: #1c2530;
  margin: 0;
  word-break: break-all;
}

.task-item__menu-btn {
  position: absolute;
  top: 4px;
  right: 3.6px;
  background: 0;
  border: 0;
  cursor: pointer;
  padding: 0;
}

.task-item__menu-btn:hover svg path {
  stroke: #3D86F4;
}
</style>