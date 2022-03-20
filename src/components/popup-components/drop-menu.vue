<template>
  <div :class="['settings__item', itemClass]">

    <slot name="name"></slot>

    <div :class="[{'settings__block': !blockClass}, blockClass, {'drop--active': dropState}]" @mouseout="toggleDrop(false)" @mouseover="toggleDrop(true)">

      <slot name="text"></slot>

      <div class="settings__right">
        <span class="settings__line"></span>
        <button class="settings__change">
          <arrow-down-icon :class="['arrow-icon', {'icon--active': dropState}]" />
        </button>
      </div>

      <ul class="drop__list" v-show="dropState">

        <slot name="item"></slot>

      </ul>

    </div>

  </div>
</template>

<script>
import { ref, watchEffect } from "vue";
import arrowDownIcon from "@/components/popup-components/icon/arrow-down-icon";

export default {
  name: "drop-menu",
  components: {
    arrowDownIcon: arrowDownIcon
  },
  props: {
    blockClass: {
      type: String,
      required: false
    },
    itemSelected: {
      type: Boolean,
      required: false
    },
    itemClass: {
      type: String,
      required: false,
      default: ''
    }
  },
  setup(props) {

    const dropState = ref(false);

    function toggleDrop(state) {
      dropState.value = state;
    }

    watchEffect(() => {
      if(props.itemSelected) {
        dropState.value = false;
      }
    })

    return { toggleDrop, dropState }

  }
}
</script>

<style scoped>

.settings__item {
  position: relative;
  width: 47%;
}

.settings__item1 {
  width: auto;
}

.settings__right {
  display: flex;
}

.settings__name {
  margin-bottom: 5px;

  font-size: 14px;
  color: #6D6D6D;
}

.settings__block {
  display: flex;
  justify-content: space-between;
  align-items: center;

  width: 100%;
  height: 48px;
  padding: 0 20px;

  border-radius: 12px;
  box-sizing: border-box;
  cursor: pointer;

  background-color: #F6F9FC;
}

.drop--active {
  border-radius: 12px 12px 0 0;
}

.settings__text {
  font-size: 16px;
}

.settings__text1 {
  margin-right: 85px;
}


.settings__line {
  display: block;
  width: 1px;
  height: 24px;
  margin-right: 15px;

  background-color: rgba(0, 0, 0, 0.1);
}

.drop__list {
  position: absolute;
  top: 66px;
  left: 0;
  z-index: 5;

  width: 100%;
  padding: 20px 0;

  box-sizing: border-box;
  border: 1px solid #F0F3F8;
  border-top: none;
  border-radius: 0 0 12px 12px;

  background-color: #fff;
}

.arrow-icon {
  transition: .1s all ease;
}

.icon--active {
  transform: rotateZ(180deg);
}

.mark-wrapper {
  display: flex;
  justify-content: space-between;
  align-items: center;

  width: 95px;
  height: 48px;
  padding: 0 15px;
  border-radius: 12px;
  box-sizing: border-box;

  background-color: #F6F9FC;
}
</style>