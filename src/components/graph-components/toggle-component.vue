<template>
  <div :class="['toggle', {'toggle--active': isActive}]" @click.stop="justToggle">
    <span :class="['toggle__eclipse', {'eclipse--active': isActive}]"></span>
  </div>
</template>

<script>

export default {
  name: "toggle-component",
  props: {
    isActive: {
      type: Boolean,
      required: false
    }
  },
  setup(props, { emit }) {

    function justToggle() {
      emit('settingToggle')
    }

    return { justToggle }
  }
}
</script>

<style scoped>
  .toggle {
    position: relative;

    width: 30px;
    height: 18px;
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