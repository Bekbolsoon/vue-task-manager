<template>
  <div class="context-menu" :ref="`contextMenu-${taskId}`">
    <button @click="deleteTask(taskId)" class="context-menu__item">
      <svg width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M2.5 4.99999H17.5M6.66667 4.99999V3.33332C6.66667 2.8913 6.84226 2.46737 7.15482 2.15481C7.46738 1.84225 7.89131 1.66666 8.33333 1.66666H11.6667C12.1087 1.66666 12.5326 1.84225 12.8452 2.15481C13.1577 2.46737 13.3333 2.8913 13.3333 3.33332V4.99999M15.8333 4.99999V16.6667C15.8333 17.1087 15.6577 17.5326 15.3452 17.8452C15.0326 18.1577 14.6087 18.3333 14.1667 18.3333H5.83333C5.39131 18.3333 4.96738 18.1577 4.65482 17.8452C4.34226 17.5326 4.16667 17.1087 4.16667 16.6667V4.99999H15.8333Z" stroke="#86949E" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
      </svg>
      Удалить
    </button>
  </div>
</template>

<script>
export default {
  props: {
    taskId: {
      type: Number,
      required: true,
    },
  },
  methods: {
    deleteTask(taskId) {
      this.$emit("delete-task", taskId);
    },
  },
};
</script>

<style scoped>
.context-menu {
  position: fixed;
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
</style>