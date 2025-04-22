<template>
  <div class="task-column">
    <h2
      class="task-column-title"
      :class="{
        'blue': section.title === 'Новые',
        'yellow': section.title === 'В процессе',
        'green': section.title === 'Готово',
        'red': section.title === 'Доработать',
      }"
    >
      {{ section.title }}
    </h2>
    <div class="task-column__inner">
      <draggable
        v-model="section.tasks"
        :group="'tasks'"
        @start="dragStart"
        @end="dragEnd"
        class="task-list">
        <TaskItem
          v-for="task in section.tasks"
          :key="task.id"
          :task="task"
          :sectionId="section.id"
          @show-context-menu="showContextMenu"
          @delete-task="deleteTask"
        />
      </draggable>

      <TaskForm
        v-if="section.showForm"
        @add-task="addTask(section.id, $event)"
        @close="cancelAddingTask(section.id)"
      />
      <button v-else @click="showTaskForm(section.id)" class="add-button">
        <svg width="15" height="14" viewBox="0 0 15 14" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M7.20002 1.16667V12.8333M1.36668 7H13.0334" stroke="#3D86F4" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
        </svg>
        Добавить
      </button>
    </div>
  </div>
</template>

<script>
import TaskItem from "./TaskItem.vue";
import TaskForm from "./TaskForm.vue";
import draggable from "vuedraggable";

export default {
  components: { TaskItem, TaskForm, draggable },
  props: {
    section: {
      type: Object,
      required: true,
    },
  },
  methods: {
    showContextMenu(taskId, sectionId, event) {
      this.$emit("show-context-menu", taskId, sectionId, event);
    },
    deleteTask(taskId, sectionId) {
      this.$emit("delete-task", taskId, sectionId);
    },
    showTaskForm(sectionId) {
      this.$emit("show-task-form", sectionId);
    },
    cancelAddingTask(sectionId) {
      this.$emit("cancel-adding-task", sectionId);
    },
    addTask(sectionId, text) {
      this.$emit("add-task", sectionId, text);
    },
    dragStart() {
      this.$emit("drag-start");
    },
    dragEnd() {
      this.$emit("drag-end");
    },
  },
};
</script>

<style scoped>
.task-column {
  width: 100%;
  border-radius: 8px;
  height: 100%;
  max-width: 177px;
  background: #f7f7f7;
  border: 1px solid #e3e5e8;
}

.task-column__inner {
  max-height: calc(100% - 31.5px);
  overflow-y: auto;
}

.task-column__inner::-webkit-scrollbar {
  width: 6px;
}

.task-column__inner::-webkit-scrollbar-track {
  background: none;
}

.task-column__inner::-webkit-scrollbar-thumb {
  border-radius: 6px;
  background: #c4cad4;
}

.task-column-title {
  font-weight: 584;
  font-size: 14px;
  line-height: 125%;
  color: #1c2530;
  background: #ff99e9;
  margin: 0;
  padding: 7px 0;
  text-align: center;
  border-radius: 8px 8px 0 0;
}

.task-column-title.blue {
  background: #66b8ff;
}
.task-column-title.yellow {
  background: #ffd466;
}
.task-column-title.green {
  background: #53c666;
}
.task-column-title.red {
  background: #f76e85;
}

.task-list {
  padding: 0 16px 0 8px;
  display: flex;
  flex-direction: column;
}

.add-button {
  font-weight: 400;
  font-size: 14px;
  line-height: 129%;
  text-align: center;
  color: #3d86f4;
  background: none;
  border: none;
  display: flex;
  align-items: center;
  gap: 4px;
  cursor: pointer;
  margin-top: 8px;
  padding: 0 8px;
}
</style>