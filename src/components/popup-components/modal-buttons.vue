<template>
  <div class="modal__setting">

    <button class="modal__close setting-button" @click="toggleNewModal('close')">Закрыть без изменений</button>
    <button class="modal__save setting-button submit__export" @click="toggleNewModal('submit')">{{ buttonText }}</button>

  </div>
</template>

<script>
export default {
  name: "modal-buttons",
  props: {
    buttonText: {
      type: String,
      required: true
    }
  },
  emits: ['toggleModal'],
  setup(props, {  emit }) {

    function toggleNewModal(type) {
      emit('toggleModal', type);
    }

    return { toggleNewModal }

  }
}
</script>

<style scoped>
.modal__setting {
  display: flex;
  justify-content: flex-end;

  margin-top: 40px;
  margin-left: auto;
}

.setting-button {
  padding: 12px 24px;
  border-radius: 12px;

  font-size: 16px;
}

.modal__close {
  margin-right: 30px;
  color: #1E2022;
  background-color: #F7F9FC;
}

.modal__close:hover {
  color: #6D6D6D;
  background-color: #F7F9FC;
}

.modal__close:active {
  color: #A1A2A2;
  background-color: #F0F3F8;
}

.modal__save {
  background-color: #FFC549;
}
</style>