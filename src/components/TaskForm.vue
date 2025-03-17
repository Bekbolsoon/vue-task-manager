<template>
  <div class="task-form">
    <textarea
        ref="textarea"
        class="task-form__input"
        placeholder="Введите текст..."
        v-model="taskText"
        @input="autoResize"
    >
    </textarea>
    <div class="task-form__btns">
      <button @click="cancelAddingTask">
        <svg width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M15 5L5 15M5 5L15 15" stroke="#F53D5C" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
      <button @click="submitTask">
        <svg width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M16.6666 5L7.49998 14.1667L3.33331 10" stroke="#22C33D" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      taskText: "",
    };
  },
  methods: {
    submitTask() {
      if (this.taskText.trim()) {
        this.$emit("add-task", this.taskText.trim());
        this.taskText = "";
      }
    },
    cancelAddingTask() {
      this.$emit("close");
    },
    autoResize(event) {
      const textarea = event.target;
      textarea.style.height = 'auto';
      textarea.style.height = `${textarea.scrollHeight - 16}px`;
    },
    focusTextarea() {
      this.$refs.textarea.focus();
    },
  },
  mounted() {
    this.focusTextarea();
  }
};
</script>

<style scoped>
button {
  cursor: pointer;
  background: none;
  border: none;
  outline: none;
}
.task-form {
  display: flex;
  flex-direction: column;
  gap: 5px;
  position: relative;
  padding: 0 16px 0 8px;
  margin-top: 8px;
}

.task-form__input {
  border: 1px solid #c4cad4;
  border-radius: 4px;
  background: #fff;
  outline: none;
  resize: none;
  overflow: hidden;
  min-height: 52px;
  padding: 8px 25px 8px 8px;
  font-weight: 400;
  font-size: 14px;
  line-height: 129%;
  color: #1c2530;
  font-family: Roboto, "Helvetica Neue", sans-serif;
}

.task-form__input:focus,
.task-form__input:focus-visible{
  border: 1px solid #3d86f4;
}

.task-form__btns {
  position: absolute;
  top: 4px;
  right: 20px;
  background: 0;
  border: 0;
  display: flex;
  flex-direction: column;
}
</style>
