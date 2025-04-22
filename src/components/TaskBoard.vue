<template>
  <div class="task-board">
    <TaskColumn
      v-for="section in sections"
      :key="section.id"
      :section="section"
      @show-context-menu="showContextMenu"
      @delete-task="deleteTask"
      @show-task-form="showTaskForm"
      @cancel-adding-task="cancelAddingTask"
      @add-task="addTask"
      @drag-start="dragStart"
      @drag-end="dragEnd"
    />

    <Modal
      :isVisible="isModalVisible"
      :taskText="taskTextToDelete"
      @close="closeModal"
      @confirm="confirmDeleteTask"
    />
  </div>
</template>

<script>
import TaskColumn from "./TaskColumn.vue";
import Modal from "./Modal.vue";

export default {
  components: { TaskColumn, Modal },
  data() {
    return {
      sections: [
        { id: 1, title: "На согласовании", tasks: [], showForm: false },
        { id: 2, title: "Новые", tasks: [], showForm: false },
        { id: 3, title: "В процессе", tasks: [], showForm: false },
        { id: 4, title: "Готово", tasks: [], showForm: false },
        { id: 5, title: "Доработать", tasks: [], showForm: false },
      ],
      taskToDelete: null,
      taskTextToDelete: "",
      isModalVisible: false,
      taskSectionId: null,
      activeContextMenu: null,
    };
  },
  mounted() {
    document.addEventListener("click", this.handleClickOutside);
  },
  beforeUnmount() {
    document.removeEventListener("click", this.handleClickOutside);
  },
  methods: {
    showContextMenu(taskId, sectionId, event) {
      this.activeContextMenu = { taskId, sectionId };
      this.$nextTick(() => {
        const button = event.target;
        const menuRef = this.$refs[`contextMenu-${taskId}`];
        if (!menuRef || !menuRef[0]) return;

        const menu = menuRef[0];
        const rect = button.getBoundingClientRect();
        menu.style.top = `${rect.bottom}px`;
        menu.style.left = `${rect.left}px`;
      });
    },
    deleteTask(taskId, sectionId) {
      const section = this.sections.find((s) => s.id === sectionId);
      const task = section.tasks.find((t) => t.id === taskId);
      this.taskToDelete = taskId;
      this.taskTextToDelete = task.text;
      this.taskSectionId = sectionId;
      this.isModalVisible = true;
      this.activeContextMenu = null;
    },
    closeModal() {
      this.isModalVisible = false;
      this.taskToDelete = null;
      this.taskSectionId = null;
      this.taskTextToDelete = "";
    },
    confirmDeleteTask() {
      const section = this.sections.find((s) => s.id === this.taskSectionId);
      const taskIndex = section.tasks.findIndex((task) => task.id === this.taskToDelete);
      if (taskIndex > -1) {
        section.tasks.splice(taskIndex, 1);
        alert(`Задача удалена`);
      }
      this.closeModal();
    },
    showTaskForm(sectionId) {
      this.sections.forEach((s) => (s.showForm = s.id === sectionId));
    },
    cancelAddingTask(sectionId) {
      const section = this.sections.find((s) => s.id === sectionId);
      section.showForm = false;
    },
    addTask(sectionId, text) {
      const section = this.sections.find((s) => s.id === sectionId);
      section.tasks.push({ id: Date.now(), text });
      section.showForm = false;
      alert(`Задача создана в "${section.title}"`);
    },
    dragStart() {
      console.log("Перетаскивание началось");
    },
    dragEnd() {
      console.log("Перетаскивание завершено");
    },
  },
};
</script>

<style scoped>
.task-board {
  display: flex;
  gap: 8px;
  width: 920px;
  height: 596px;
  margin: 0 auto;
  margin-top: 48px;
}
</style>
