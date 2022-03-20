<template>

  <div>
    <transition-group name="fade">
      <div :class="['modal', parentClass]" v-show="modalState" key="1">
        <slot></slot>
      </div>

      <div class="modal-close" @click="closeModal" v-show="modalState" key="2"></div>
    </transition-group>
  </div>

</template>

<script>

export default {
  name: "modal-wrapper",
  props: {
    parentClass: {
      type: String,
      required: false
    },
    modalState: {
      type: Boolean,
      required: false
    }
  },
  emits: ['closeModal'],
  setup(props, { emit }) {

    function closeModal() {
      emit('closeModal');
    }

    return { closeModal }
  }
}
</script>

<style scoped>

.modal {
  position: absolute;
  top: calc(50vh - 370px);
  left: 50%;
  z-index: 5;

  width: 730px;
  box-sizing: border-box;
  padding: 40px;
  box-shadow: 0px 24px 120px -30px rgba(0, 0, 0, 0.15);
  border-radius: 30px;
  background-color: #fff;

  transform: translateX(-50%);
}

.new__modal {
  top: calc(50vh - 340px);

  max-width: 600px;
}

.editor-modal {
  top: calc(50vh - 390px);
}

.modal-close {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 4;

  width: 100%;
  height: 100vh;
}

.fade-enter-active,
.fade-leave-active {
  transition: all 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  top: 100px;
  opacity: 0;
}
</style>