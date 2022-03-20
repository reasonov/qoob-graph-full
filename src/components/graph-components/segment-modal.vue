<template>
  <div class="segment-modal">
    <h3 class="modal__title">Настройка диапазона</h3>

    <div class="modal__item">
      <p class="modal__name">Количество покупок</p>

      <div class="data__block">
        <input
            class="modal__data modal__data1"
            type="text"
            placeholder="1"
            disabled
        >
        &mdash;
        <input
            class="modal__data modal__data2"
            v-model="modalData.endY"
            type="text"
            placeholder="5"
        >
      </div>

    </div>

    <div class="modal__item">
      <p class="modal__name">Давность, дней</p>

      <div class="data__block">
        <input
            class="modal__data modal__data1"
            type="text"
            placeholder="0"
            disabled
        >
        &mdash;
        <input
            class="modal__data modal__data2"
            v-model="modalData.endX"
            type="text"
            placeholder="160"
        >
      </div>

    </div>

    <div class="modal__buttons">
      <button class="modal__close" @click="closeModal">Закрыть</button>
      <button class="modal__save" @click="saveModalData">Сохранить изменения</button>
    </div>

  </div>
</template>

<script>
import { reactive } from "vue";

export default {
  name: "segment-modal",
  props: {
    numberOfX: {
      type: Object,
      required: true
    },
    numberOfY: {
      type: Object,
      required: true
    }
  },
  setup(props, { emit }) {

    const modalData = reactive({
      endX: props.numberOfX.endX,
      endY: props.numberOfY.endY
    })

    function saveModalData() {
      emit('takeModalData', modalData)

      closeModal();
    }

    function closeModal() {
      emit('closeModal');
    }

    return { modalData, closeModal, saveModalData }
  }
}
</script>

<style scoped>
  .segment-modal {
    position: fixed;
    top: 50%;
    left: 50%;
    z-index: 10;

    width: 547px;
    padding: 60px;

    transform: translateX(-50%) translateY(-50%);
    box-shadow: 0px 24px 120px -30px rgba(0, 0, 0, 0.15);
    border-radius: 30px;
    box-sizing: border-box;

    background-color: #fff;
  }

  .modal__title {
    margin-bottom: 38px;

    font-size: 26px;
    font-weight: 600;
    color: #1E2022;
  }

  .modal__item {
    display: flex;
    justify-content: space-between;
    align-items: center;

    margin-bottom: 20px;
  }

  .modal__name {
    font-size: 16px;
    font-weight: 600;
    color: #1E2022;
  }

  .data__block {
    display: flex;
    align-items: center;
  }

  .modal__data {
    display: block;
    width: 30px;
    height: 24px;
    padding: 8px 15px;

    border-radius: 12px;

    font-size: 14px;
    text-align: center;

    background-color: #F7F9FC;
  }

  .modal__data1 {
    margin-right: 10px;
  }

  .modal__data2 {
    margin-left: 10px;
  }

  .modal__buttons {
    display: flex;
    justify-content: space-between;

    width: 350px;
    margin-top: 50px;
    margin-left: auto;
  }

  .modal__close {
    width: 112px;
    height: 48px;

    border-radius: 12px;

    font-size: 16px;
    color: #1E2022;

    background: #F7F9FC;
  }

  .modal__save {
    width: 219px;
    height: 48px;

    border-radius: 12px;

    font-size: 16px;
    color: #000;

    background: #FFC549;
  }
</style>