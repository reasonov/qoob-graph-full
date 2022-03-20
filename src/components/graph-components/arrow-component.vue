<template>
  <div class="arrow" @mousedown="activateArrow()">
    <arrow-icon />
  </div>
</template>

<script>
import arrowIcon from './icons/arrow-icon'
import { ref, reactive } from 'vue'

export default {
  name: "arrow-component",
  components: {
    arrowIcon: arrowIcon
  },
  props: {
    index: {
      type: Number,
      required: true
    }
  },
  setup(props, { emit }) {

    const isActive = ref(false);

    let positionList = reactive([]);

    window.addEventListener('mouseup', deactivateArrow);

    function activateArrow() {
      isActive.value = true;

      window.addEventListener('mousemove', moveArrow);
    }

    function throttle(func, ms) {

      let isThrottled = false;

      function wrapper() {

        if(isThrottled) {
          return;
        }

        func.apply(this, arguments);

        isThrottled = true;

        setTimeout(function() {
          isThrottled = false;
        }, ms);
      }

      return wrapper;

    }

    const throttleTop = throttle(() => {

      if(positionList[positionList.length - 1] < positionList[positionList.length - 2]) {
        emit('changesFromArrow', 1)
      }


    }, 500);

    const throttleBottom = throttle(() => {

      if(positionList[positionList.length - 1] > positionList[positionList.length - 2]) {
        emit('changesFromArrow', -1)
      }

    }, 500);

    function moveArrow(event) {

      event.preventDefault();

      if(props.index === 1 || props.index === 3) {

        const throttlePush = throttle(() => {
          positionList.push(event.clientY);
        }, 500)

        throttlePush();

        throttleTop();

        throttleBottom();

      } else {

        positionList.push(event.clientX);

        if(positionList[positionList.length - 1] > positionList[positionList.length - 2]) {
          emit('changesFromArrow', 1);
        } else {
          emit('changesFromArrow', -1);
        }

      }

    }

    function deactivateArrow() {
      isActive.value = false;
      positionList = [];

      window.removeEventListener('mousemove', moveArrow)
    }

    return { activateArrow }
  }
}
</script>

<style scoped>
  .arrow {
    display: flex;
    justify-content: center;
    align-items: center;

    width: 24px;
    height: 24px;

    border-radius: 12px 12px 0px 0px;
    cursor: pointer;

    background-color: #fff;
  }
</style>