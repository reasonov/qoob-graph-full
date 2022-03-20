<template>

  <main-layout @toggleMainModal="toggleMainModal($event)" />

  <transition name="popupAm">
    <popup-layout
        class="popup-wrapper"
        @toggleMainModal="toggleMainModal($event)"
        :segmentPopup="segmentPopup"
        v-show="modalState"
    />
  </transition>

</template>

<script>
import { ref } from "vue";
import Popup from './layouts/popup';
import mainLayout from "@/layouts/main-layout";

export default {
  components: {
    mainLayout: mainLayout,
    PopupLayout: Popup
  },
  setup() {

    const modalState = ref(false);
    const segmentPopup = ref(2);

    function toggleMainModal(data) {
      if(data.state) {
        modalState.value = data.state;
        segmentPopup.value = data.index + 1;
      } else {
        modalState.value = data;
      }

    }

    return { modalState, toggleMainModal, segmentPopup }

  }
}

</script>

<style scoped>

  .popup-wrapper {
    position: absolute;
    right: 0;
    top: 0;
  }

  .popupAm-enter-active,
  .popupAm-leave-active {
    transition: .5s all ease;
  }

  .popupAm-enter-from,
  .popupAm-leave-to {
    left: 100%;
    opacity: 0;
  }
</style>
