<template>
  <div v-if="isVisible" class="modal">
    <div class="modal-content">
      <div class="modal-content__btn-close" @click="close">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M18 6L6 18M6 6L18 18" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
        </svg>
      </div>
      <h2 class="modal-content__title">Удалить задачу?</h2>
      <p class="modal-content__text">{{ taskText }}</p>
      <div class="modal-content__btns">
        <button @click="confirm">Удалить</button>
        <button @click="close">Отмена</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    isVisible: {
      type: Boolean,
      required: true,
    },
    taskText: {
      type: String,
      default: "",
    },
  },
  methods: {
    close() {
      this.$emit("close");
    },
    confirm() {
      this.$emit("confirm");
    },
  },
};
</script>

<style scoped>
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