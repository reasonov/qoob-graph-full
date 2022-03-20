<template>

  <div class="item__section item__section1">
    <button class="notif-item__move">
      <span class="move__line"></span>
      <span class="move__line"></span>
      <span class="move__line"></span>
    </button>

    <sms-tracking-icon class="notif__icon" />

    <p class="notif-item__text">{{ itemText }}</p>
  </div>

  <div class="item__section">
    <toggle-component class="toggle-component" />

    <div class="action-wrapper">

      <button class="notif__action notif__action1" @click="toggleNewModal">
        <edit-icon />
      </button>

      <button class="notif__action" @click="deleteItem">
        <close-square />
      </button>

    </div>
  </div>

</template>

<script>
import editIcon from "@/components/popup-components/icon/edit-icon";
import smsTrackingIcon from "@/components/popup-components/icon/sms-tracking-icon";
import toggleComponent from "@/components/popup-components/toggle-component";
import closeSquare from "@/components/popup-components/icon/close-square";

export default {
  name: "notification-item",
  components: {
    smsTrackingIcon: smsTrackingIcon,
    toggleComponent: toggleComponent,
    editIcon: editIcon,
    closeSquare: closeSquare
  },
  props: {
    itemIndex: {
      type: Number,
      required: true
    },
    itemText: {
      type: String,
      required: true
    }
  },
  emits: ['takeNewModal', 'deleteItem'],
  setup(props, { emit }) {

    function toggleNewModal() {
      emit('takeNewModal', {
        modal: 'editNotification',
        state: true,
        index: props.itemIndex
      });
    }

    function deleteItem() {
      emit('deleteItem', props.itemIndex);
    }

    return { toggleNewModal, deleteItem }
  }
}
</script>

<style scoped>
  .item__section {
    display: flex;
    align-items: center;
  }

  .item__section1 {
    min-height: 27px;
    margin-right: 30px;
  }

  .notif-item__move {
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    width: 12px;
    height: 10px;
    margin-right: 25px;
  }

  .move__line {
    width: 100%;
    height: 2px;
    border-radius: 5px;

    background-color: #A1A2A2;
  }

  .notif__icon {
    margin-right: 22px;
  }

  .notif-item__text {
    font-size: 14px;
    color: #1E2022;
  }

  .toggle-component {
    margin-right: 50px;
  }

  .action-wrapper {
    display: flex;
  }

  .notif__action {
    width: 40px;
    height: 40px;

    border: 2px solid #FFC549;
    border-radius: 12px;
  }

  .notif__action1 {
    margin-right: 20px;
  }

  @media (max-width: 1580px) {
    .toggle-component {
      margin-right: 20px;
    }

    .notif__action1 {
      margin-right: 10px;
    }

    .notif-item__move {
      margin-right: 15px;
    }
    
    .notif-item__text {
      max-width: 250px;
    }
  }


</style>