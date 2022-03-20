<template>

  <modal-wrapper @closeModal="takeModalState('close')" parentClass="editor-modal" :modalState="notifEditorState">

    <div class="modal__top">
      <h4 class="modal__title title">Редактор типов уведомлений</h4>

      <div class="body__header">

        <div class="input-wrapper">
          <label class="set-name__label header__text" for="set-name">Название типа уведомлений</label>
          <input id="set-name" type="text" :placeholder="placeholderText" v-model="itemName">
        </div>

        <drop-menu blockClass="mark-wrapper" :itemSelected="itemSelected" itemClass="settings__item1">

          <template v-slot:name>
            <p class="settings__name">
              Метка\Номер
            </p>
          </template>

          <template v-slot:text>
            <p class="settings__text" v-if="currentMark === 0">
              <star-icon class="modal__star" />
            </p>
            <p class="settings__text" v-else-if="currentMark === 1">
              <emoji-normal-icon />
            </p>
            <p class="settings__text" v-else-if="currentMark === 2">
              <trend-up-icon />
            </p>
            <p class="settings__text" v-else-if="currentMark === 3">
              <cake-icon />
            </p>
            <p class="settings__text" v-else>
              <sms-tracking-icon />
            </p>
          </template>

          <template v-slot:item>

            <li>
              <button class="drop__item" @click="selectItem(0, 'mark')">
                <star-icon />
              </button>
            </li>

            <li>
              <button class="drop__item" @click="selectItem(1, 'mark')">
                <emoji-normal-icon />
              </button>
            </li>

            <li>
              <button class="drop__item" @click="selectItem(2, 'mark')">
                <trend-up-icon />
              </button>
            </li>

            <li>
              <button class="drop__item" @click="selectItem(3, 'mark')">
                <cake-icon />
              </button>
            </li>

            <li>
              <button class="drop__item" @click="selectItem(4, 'mark')">
                <sms-tracking-icon />
              </button>
            </li>

          </template>

        </drop-menu>

      </div>
    </div>

    <modal-main />

    <modal-buttons buttonText="Сохранить изменения" @toggleModal="takeModalState($event)"></modal-buttons>

  </modal-wrapper>

</template>

<script>
import { ref, computed } from "vue";
import modalWrapper from "@/components/popup-components/modal-wrapper";
import modalMain from "@/components/popup-components/modal-main";
import modalButtons from "@/components/popup-components/modal-buttons";
import starIcon from "@/components/popup-components/icon/star-icon";
import dropMenu from "@/components/popup-components/drop-menu";
import emojiNormalIcon from "@/components/popup-components/icon/emoji-normal-icon";
import cakeIcon from "@/components/popup-components/icon/cake-icon";
import smsTrackingIcon from "@/components/popup-components/icon/sms-tracking-icon";
import trendUpIcon from "@/components/popup-components/icon/trend-up-icon";

export default {
  name: "notification-editor",
  components: {
    modalWrapper: modalWrapper,
    modalMain: modalMain,
    modalButtons: modalButtons,
    starIcon: starIcon,
    dropMenu: dropMenu,
    emojiNormalIcon: emojiNormalIcon,
    cakeIcon: cakeIcon,
    smsTrackingIcon: smsTrackingIcon,
    trendUpIcon: trendUpIcon
  },
  props: {
    currentItem: {
      type: Object,
      required: false
    },
    notifEditorState: {
      type: Boolean,
      required: false
    }
  },
  emits: ['toggleModal'],
  setup(props, { emit }) {

    const placeholderText = computed(() => {
      if(props.currentItem) {
        return props.currentItem.text;
      }
      return '';
    })

    const currentMark = ref(0);

    const itemSelected = ref(false);

    function selectItem(index) {

      currentMark.value = index;

      itemSelected.value = true;
      setTimeout(() => {
        itemSelected.value = false;
      }, 500);

    }

    function takeModalState(type) {
      emptyName();

      if(type === 'submit') {
        emit('toggleModal', {
          modal: 'notificationEditor',
          updateName: itemName.value,
          state: false,
          type: type
        })
      } else {
        emit('toggleModal', {
          modal: 'notificationEditor',
          state: false
        })
      }

    }

    const itemName = ref('');
    function emptyName() {

      if(itemName.value === '' || itemName.value === ' ') {
        itemName.value = props.currentItem.text;
      }

    }


    return {
      takeModalState,
      selectItem,
      currentMark,
      itemSelected,
      itemName,
      placeholderText
    }

  }
}
</script>

<style scoped>
.modal__top {
  padding-bottom: 111px;
  border-bottom: 1px solid #EFF0F0;
}

.modal__title {
  margin-bottom: 30px;
}

.body__header {
  display: flex;
  justify-content: space-between;
}

.set-name__label {
  font-size: 14px;
  color: #6D6D6D;
}

#set-name {
  width: 95%;
  height: 48px;

  padding: 12px 20px;
  border-radius: 12px;
  box-sizing: border-box;

  background-color: #F6F9FC;
}

.input-wrapper {
  width: 100%;
}

.header__text {
  display: block;
  margin-bottom: 5px;
}

.mark__text {
  font-size: 14px;
  color: #6D6D6D;
}

.modal__star {
  stroke: #A1A2A2;
}

.mark__line {
  width: 1px;
  height: 24px;

  background-color: rgba(0, 0, 0, 0.1);
}

.segment-settings {
  display: flex;
  justify-content: space-between;
  margin-top: 35px;
}

.settings__name {
  margin-bottom: 5px;

  font-size: 14px;
  color: #6D6D6D;
}

.settings__block {
  display: flex;
  align-items: center;

  width: 377px;
  height: 48px;
  padding: 0 20px;
  border-radius: 12px;
  box-sizing: border-box;

  background-color: #F6F9FC;
}

.settings__number {
  width: 24px;
  height: 24px;
  margin-right: 10px;

  border-radius: 9px;

  font-size: 12px;
}

.settings__text {
  font-size: 16px;
}

.settings__line {
  width: 1px;
  height: 24px;
  margin-right: 15px;
  margin-right: 15px;

  background-color: rgba(0, 0, 0, 0.1);
}

.flex {
  display: flex;
  align-items: center;
}

.drop__item {
  display: flex;
  align-items: center;

  height: 30px;
  margin-bottom: 20px;

  font-size: 15px;
}

.drop__item--last {
  margin-bottom: 0;
}

</style>