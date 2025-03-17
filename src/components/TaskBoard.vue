<template>
  <div class="task-board">
    <div v-for="section in sections" :key="section.id" class="task-column">
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
          <div v-for="task in section.tasks" :key="task.id" class="task-item">
            <p class="task-item-text">{{ task.text }}</p>
            <button
              @click="showContextMenu(task.id, section.id)"
              class="task-item__menu-btn"
              :ref="`menuButton-${task.id}`">
              <svg width="21" height="20" viewBox="0 0 21 20" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M10.2 10.8333C10.6603 10.8333 11.0334 10.4602 11.0334 10C11.0334 9.53977 10.6603 9.16667 10.2 9.16667C9.73978 9.16667 9.36669 9.53977 9.36669 10C9.36669 10.4602 9.73978 10.8333 10.2 10.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
                <path d="M16.0334 10.8333C16.4936 10.8333 16.8667 10.4602 16.8667 10C16.8667 9.53977 16.4936 9.16667 16.0334 9.16667C15.5731 9.16667 15.2 9.53977 15.2 10C15.2 10.4602 15.5731 10.8333 16.0334 10.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
                <path d="M4.36669 10.8333C4.82693 10.8333 5.20002 10.4602 5.20002 10C5.20002 9.53977 4.82693 9.16667 4.36669 9.16667C3.90645 9.16667 3.53336 9.53977 3.53336 10C3.53336 10.4602 3.90645 10.8333 4.36669 10.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
              </svg>
            </button>

            <!-- Контекстное меню -->
            <div
              v-if="activeContextMenu && activeContextMenu.taskId === task.id && activeContextMenu.sectionId === section.id"
              class="context-menu"
              :ref="`contextMenu-${task.id}`">
              <button @click="deleteTask(task.id, section.id)" class="context-menu__item">
                <svg width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M2.5 4.99999H17.5M6.66667 4.99999V3.33332C6.66667 2.8913 6.84226 2.46737 7.15482 2.15481C7.46738 1.84225 7.89131 1.66666 8.33333 1.66666H11.6667C12.1087 1.66666 12.5326 1.84225 12.8452 2.15481C13.1577 2.46737 13.3333 2.8913 13.3333 3.33332V4.99999M15.8333 4.99999V16.6667C15.8333 17.1087 15.6577 17.5326 15.3452 17.8452C15.0326 18.1577 14.6087 18.3333 14.1667 18.3333H5.83333C5.39131 18.3333 4.96738 18.1577 4.65482 17.8452C4.34226 17.5326 4.16667 17.1087 4.16667 16.6667V4.99999H15.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
                </svg>
                Удалить
              </button>
            </div>
          </div>
        </draggable>

        <!-- Добавление новой задачи -->
        <TaskForm v-if="section.showForm" @add-task="addTask(section.id, $event)" @close="cancelAddingTask(section.id)" />
        <button v-else @click="showTaskForm(section.id)" class="add-button">
          <svg width="15" height="14" viewBox="0 0 15 14" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M7.20002 1.16667V12.8333M1.36668 7H13.0334" stroke="#3D86F4" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
          </svg>
          Добавить
        </button>
      </div>
    </div>

    <!-- Модальное окно для удаления задачи -->
    <div v-if="isModalVisible" class="modal">
      <div class="modal-content">
        <div class="modal-content__btn-close" @click="closeModal">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M18 6L6 18M6 6L18 18" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
          </svg>
        </div>
        <h2 class="modal-content__title">Удалить задачу?</h2>
        <p class="modal-content__text">{{ taskTextToDelete }}</p>
        <div class="modal-content__btns">
          <button @click="confirmDeleteTask">Удалить</button>
          <button @click="closeModal">Отмена</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import TaskForm from "./TaskForm.vue";
import draggable from "vuedraggable";

export default {
  components: { TaskForm, draggable },
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
    // Обработка клика для закрытия контексого меню
    handleClickOutside(event) {
      if (!this.activeContextMenu) return;

      const { taskId } = this.activeContextMenu;

      const contextMenu = this.$refs[`contextMenu-${taskId}`];
      const menuButton = this.$refs[`menuButton-${taskId}`];

      // Если ref возвращает массив, берем первый элемент
      const contextMenuElement = Array.isArray(contextMenu) ? contextMenu[0] : contextMenu;
      const menuButtonElement = Array.isArray(menuButton) ? menuButton[0] : menuButton;

      // Если контекстное меню или кнопка меню не существуют, выходим
      if (!contextMenuElement || !menuButtonElement) return;

      // Если клик был вне меню и не на кнопке меню, закрываем меню
      if (
          !contextMenuElement.contains(event.target) &&
          !menuButtonElement.contains(event.target)
      ) {
        this.activeContextMenu = null;
      }
    },

    // Показать/скрыть контекстное меню
    showContextMenu(taskId, sectionId) {
      if (
        this.activeContextMenu &&
        this.activeContextMenu.taskId === taskId &&
        this.activeContextMenu.sectionId === taskId
      ) {
        // Если меню уже открыто, закрыть его
        this.activeContextMenu = null;
      } else {
        // Если нет, открыть его
        this.activeContextMenu = { taskId, sectionId };
      }
    },

    // Отображение модального окна для удаления задачи
    deleteTask(taskId, sectionId) {
      const section = this.sections.find((s) => s.id === sectionId);
      const task = section.tasks.find((t) => t.id === taskId);
      this.taskToDelete = taskId;
      this.taskTextToDelete = task.text;
      this.taskSectionId = sectionId;
      this.isModalVisible = true;
      this.activeContextMenu = null;
    },

    // Закрытие модального окна для удаления задачи
    closeModal() {
      this.isModalVisible = false;
      this.taskToDelete = null;
      this.taskSectionId = null;
      this.taskTextToDelete = "";
    },

    // Удаление задачи
    confirmDeleteTask() {
      const section = this.sections.find((s) => s.id === this.taskSectionId);
      const taskIdToDelete = Number(this.taskToDelete);
      const taskIndex = section.tasks.findIndex((task) => {
        return task.id === taskIdToDelete;
      });

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

.context-menu {
  position: absolute;
  top: 31px;
  right: 24px;
  transform: translateX(100%);
  border-radius: 4px;
  z-index: 10;
  padding: 8px 0;
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.06), 0 8px 8px 0 rgba(0, 0, 0, 0.08);
  background: #fff;
  border: 1px solid #e3e5e8;
}

.context-menu__item {
  display: flex;
  gap: 8px;
  align-items: center;
  width: 100%;
  padding: 5px 10px;
  text-align: left;
  background: none;
  border: none;
  cursor: pointer;
  font-weight: 400;
  font-size: 14px;
  line-height: 129%;
  color: #1c2530;
}

.context-menu__item:hover {
  background: #e1f1ff;
}

/* Стиль для модального окна */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  border-radius: 8px;
  padding: 24px 40px 40px 40px;
  box-shadow: 0 32px 48px 0 rgba(0, 0, 0, 0.16), 0 16px 32px 0 rgba(0, 0, 0, 0.1);
  background: #fff;
  min-width: 500px;
  position: relative;
}

.modal-content__btn-close {
  position: absolute;
  right: 16px;
  top: 16px;
  cursor: pointer;
}

.modal-content__title {
  font-weight: 584;
  font-size: 24px;
  line-height: 125%;
  color: #1c2530;
  margin-bottom: 24px;
}

.modal-content__text {
  font-weight: 400;
  font-size: 14px;
  line-height: 129%;
  color: #1c2530;
  margin-bottom: 24px;
}

.modal-content__btns {
  display: flex;
  gap: 16px;
}

.modal-content__btns button {
  font-weight: 400;
  font-size: 14px;
  line-height: 129%;
  text-align: center;
  color: #1c2530;
  background: none;
  border: 1px solid #c4cad4;
  border-radius: 4px;
  padding: 8px 16px;;
  width: calc(50% - 8px);
  cursor: pointer;
}
</style>
