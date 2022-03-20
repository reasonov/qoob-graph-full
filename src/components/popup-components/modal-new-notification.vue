<template>
  <modal-wrapper parentClass="new__modal" @closeModal="takeButtonState('close')" :modalState="newModalState">
    <h4 class="new__title title">Новый тип уведомлений для сегмента</h4>

    <button class="add-type">
      <span class="add-type__text">Создать новый тип уведомлений для сегмента</span>
      <add-icon class="new__add-icon" />
    </button>

    <h4 class="new__subtitle">Добавить готовый</h4>

    <ul class="new__list">

      <li class="new__item" v-for="(item, index) in notificationList" :key="index">
        <div class="item-left">
          <sms-tracking-icon class="new__sms-icon" />

          <p class="new__text">{{ item.text }}</p>
        </div>

        <custom-checkbox :item="item" :index="item.index" @toggleCheckbox="takeCheckbox($event)" />
      </li>

    </ul>

    <modal-buttons buttonText="Сохранить" @toggleModal="takeButtonState($event)" />

  </modal-wrapper>
</template>

<script>
import { reactive } from 'vue';
import modalWrapper from "@/components/popup-components/modal-wrapper";
import customCheckbox from "@/components/popup-components/custom-checkbox";
import addIcon from "@/components/popup-components/icon/add-icon";
import smsTrackingIcon from "@/components/popup-components/icon/sms-tracking-icon";
import modalButtons from "@/components/popup-components/modal-buttons";

export default {
  name: "modal-new-segment",
  components: {
    modalWrapper: modalWrapper,
    customCheckbox: customCheckbox,
    addIcon: addIcon,
    smsTrackingIcon: smsTrackingIcon,
    modalButtons: modalButtons
  },
  props: {
    newModalState: {
      type: Boolean,
      required: false
    }
  },
  emits: ['closePopup', 'addNotification'],
  setup(props, { emit }) {

    const notificationList = reactive([
      {
        index: 0,
        text: 'Напоминание клиенту о предстоящем визите',
        state: false
      },
      {
        index: 1,
        text: 'Запрос отзыва после визита',
        state: false
      },
      {
        index: 2,
        text: 'Создание записи через виджет онлайн-записи',
        state: false
      },
      {
        index: 3,
        text: 'Поздавление клиента с днем рождения',
        state: false
      },
      {
        index: 4,
        text: 'Предупреждение клиента об окончании действия скидки',
        state: false
      },
      {
        index: 5,
        text: 'Массовые рассылки клиентам',
        state: false
      }
    ]);

    function takeCheckbox(data) {

      if(data.state) {
        notificationList[data.index].state = true;
      } else {
        notificationList[data.index].state = false;
      }

    }

    function takeButtonState(type) {
      if(type === 'close') {
        emit('closePopup');
      } else {
        emit('addNotification', {
          modal: 'newNotification',
          state: false,
          list: notificationList
        });
      }
    }

    return { notificationList, takeButtonState, takeCheckbox }

  }
}
</script>

<style scoped>

.new__title {
  margin-bottom: 45px;
}

.add-type {
  display: flex;
  justify-content: center;
  align-items: center;

  width: 100%;
  height: 60px;
  padding: 0 30px;
  margin-bottom: 50px;

  border: 2px dashed #A1A2A2;
  border-radius: 30px;
  box-sizing: border-box;
}

.add-type__text {
  display: block;
  margin-right: 70px;

  font-size: 16px;
  color: #A1A2A2;
}

.new__add-icon {
  stroke: #A1A2A2;
}

.new__subtitle {
  margin-bottom: 20px;

  font-size: 16px;
  color: #1E2022;
}

.new__list {
  margin-bottom: 60px;
}

.new__item {
  display: flex;
  justify-content: space-between;
  align-items: center;

  height: 48px;

  border-bottom: 1px solid #EFF0F0;

  font-size: 14px;
}

.item-left {
  display: flex;
  align-items: center;
}

.new__text {
  margin-left: 15px;
}

.new__sms-icon {
  stroke: #FED47A;
}

</style>