<template>
  <div :class="['checkbox', {'checkbox--active': item.state}]" @click="toggleCheckbox">
    <tick-item-icon v-show="item.state" />
  </div>
</template>

<script>
import { ref, watch } from "vue";
import tickItemIcon from "@/components/popup-components/icon/tick-item-icon";

export default {
  name: "custom-checkbox",
  components: {
    tickItemIcon: tickItemIcon
  },
  props: {
    index: {
      type: Number,
      required: true
    },
    item: {
      type: Object,
      required: true
    }
  },
  emits: ['toggleCheckbox'],
  setup(props, { emit }) {
    const checkboxState = ref(false);

    function toggleCheckbox() {
      checkboxState.value = !checkboxState.value;
    }

    watch(checkboxState, () => {
      emit('toggleCheckbox', {
        index: props.index,
        state: checkboxState.value
      });
    })

    return { toggleCheckbox }
  }
}
</script>

<style scoped>
  .checkbox {
    display: flex;
    justify-content: center;
    align-items: center;

    width: 22px;
    height: 22px;

    border: 1px solid #A1A2A2;
    border-radius: 8px;
    box-sizing: border-box;
    transition: .2s all ease;
  }

  .checkbox--active {
    border: 1px solid #FFC549;
  }
</style>