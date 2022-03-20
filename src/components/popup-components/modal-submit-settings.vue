<template>
  <modal-wrapper @closeModal="toggleSubmitModal('close')" :modalState="submitModalState">
    <h4 class="submit__title title">Ручная отправка уведомлений</h4>

    <h4 class="submit__subtitle">Дополнительный фильтр</h4>

    <div class="segment-settings">

      <drop-menu :itemSelected="itemSelected">

        <template v-slot:name>
          <p class="settings__name">
            Выпадающий список
          </p>
        </template>

        <template v-slot:text>
          <p class="settings__text">
            {{ list1[currentItem1] }}
          </p>
        </template>

        <template v-slot:item>

          <li v-for="(item, index) in list1" :key="item" @click="selectItem(index, 'list1')">
            <button :class="['drop__item', {'drop__item--last': index === list1.length}]">{{ item }}</button>
          </li>

        </template>

      </drop-menu>

      <drop-menu :itemSelected="itemSelected">

        <template v-slot:name>
          <p class="settings__name">
            Выпадающий список
          </p>
        </template>

        <template v-slot:text>
          <p class="settings__text">
            {{ list2[currentItem2] }}
          </p>
        </template>

        <template v-slot:item>

          <li v-for="(item, index) in list2" :key="item" @click="selectItem(index, 'list2')">
            <button :class="['drop__item', {'drop__item--last': index === list2.length}]">{{ item }}</button>
          </li>

        </template>

      </drop-menu>
    </div>

    <modal-main />

    <modal-buttons buttonText="На отправку" @toggleModal="toggleSubmitModal($event)"></modal-buttons>

  </modal-wrapper>
</template>

<script>
import { ref } from "vue";
import modalWrapper from "@/components/popup-components/modal-wrapper";
import modalMain from "@/components/popup-components/modal-main";
import modalButtons from "@/components/popup-components/modal-buttons";
import dropMenu from "@/components/popup-components/drop-menu";

export default {
  name: "modal-submit-settings",
  components: {
    modalWrapper: modalWrapper,
    modalMain: modalMain,
    dropMenu: dropMenu,
    modalButtons: modalButtons
  },
  props: {
    submitModalState: {
      type: Boolean,
      required: false
    }
  },
  emits: ['toggleModal'],
  setup(props, { emit }) {
    function toggleSubmitModal(type) {

      emit('toggleModal', {
        modal: 'submitSettings',
        state: false,
        type: type
      });

    }

    const list1 = [
        'Пункт 1',
        'Пункт 2'
    ]

    const list2 = [
        'Пункт 3',
        'Пункт 4',
        'Пункт 5'
    ]

    const currentItem1 = ref(0);
    const currentItem2 = ref(0);

    const itemSelected = ref(false);

    function selectItem(index, list) {

      if(list === 'list1') {
        currentItem1.value = index;
      }  else {
        currentItem2.value = index;
      }

      itemSelected.value = true;
      setTimeout(() => {
        itemSelected.value = false;
      }, 500);

    }

    return { toggleSubmitModal, list1, list2, currentItem1, currentItem2, selectItem, itemSelected }
  }
}
</script>

<style scoped>

.submit__title {
  margin-bottom: 30px;
}

.segment-settings {
  display: flex;
  justify-content: space-between;
  margin-top: 25px;
  padding-bottom: 50px;

  border-bottom: 1px solid #EFF0F0;
}

.drop__item {
  display: flex;
  align-items: center;

  height: 25px;
  margin-bottom: 20px;

  font-size: 15px;
}

.drop__item--last {
  margin-bottom: 0;
}

.settings__name {
  margin-bottom: 5px;

  font-size: 14px;
  color: #6D6D6D;
}

.settings__text {
  font-size: 16px;
}

</style>