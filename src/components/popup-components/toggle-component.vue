<template>
  <div :class="['toggle', {'toggle--active': toggle}]" @click="justToggle">
    <span :class="['toggle__eclipse', {'eclipse--active': toggle}]"></span>
  </div>
</template>

<script>
import { ref } from "vue";

export default {
  name: "toggle-component",
  setup() {

    const toggle = ref(true);

    function justToggle() {
      toggle.value = !toggle.value;
    }

    return { toggle, justToggle }
  }
}
</script>

<style scoped>
.toggle {
  position: relative;
  width: 30px;
  height: 18px;
  cursor: pointer;
  border-radius: 51px;
  transition: .3s all ease;
  background-color: #EDF2FB;
}

.toggle__eclipse {
  position: absolute;
  top: 3px;
  left: 3px;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.2);
  transition: .3s all ease;
  background-color: #FFFFFF;
}
.toggle--active {
  background-color: #FFC549;
}
.eclipse--active {
  left: 14px;
}
</style>