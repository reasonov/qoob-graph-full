<template>
  <div
      :class="[
          'item__background',
          'item__background' + itemIndex,
          borderClass,
          {'border-top': backgroundIndex === 0},
          {'border-bottom': backgroundIndex === numberOfBackground - 1},
          {'settings--active': itemIndex === currentItem && settingsState},
          {'current-item': itemIndex === currentItem}
      ]"
      :style="itemStyle"
  >
  </div>
</template>

<script>
import { computed } from "vue";

export default {
  name: "item-background",
  props: {
    itemIndex: {
      type: Number,
      required:true
    },
    backgroundIndex: {
      type: Number,
      required: true
    },
    itemState: {
      type: Object,
      required: true
    },
    numberOfBackground: {
      type: Number,
      required: true
    },
    settingsState: {
      type: Boolean,
      required: true
    },
    currentItem: {
      type: Number,
      required: false
    }
  },
  setup(props) {

    const borderClass = computed(() => {

      if((props.itemIndex === 0 || props.itemIndex === 4) && props.backgroundIndex === 0) {
        return 'border' + props.itemIndex;
      } else if((props.itemIndex === 3 || props.itemIndex == 8) && props.backgroundIndex === props.numberOfBackground - 1) {
        return 'border' + props.itemIndex;
      }
      return '';

    })

    const itemStyle = computed(() => {

      let style = 'height: ' + props.itemState.height + 'px; ';

      if(props.itemState.width === 100) {
        style += 'width: 100%;'
      } else {
        style += 'width: ' + props.itemState.width + 'px; ';
      }

      if(props.settingsState) {
        style += 'background-color: #eaf2ff; ';
      }

      style += 'left: ' + props.itemState.position + 'px; ';

      return style;

    })

    return { borderClass, itemStyle }

  }
}
</script>

<style scoped>
.item__background {
  position: relative;
  
  box-sizing: border-box;
  border-left: 8px solid #fff;
  border-right: 8px solid #fff;
  transition: .5s background-color ease;
}

.border-top {
  border-top: 8px solid #fff;
}

.border-bottom {
  border-bottom: 8px solid #fff;
}

.border-top::before {
  content: '';
  position: absolute;
  top: -9px;
  left: -9px;

  width: calc(100% + 18px);
  height: 8px;

  background-color: #fff;
}

.current-item::before {
  content: '';
  z-index: 5;
}

.current-item::after {
  content: '';
  z-index: 5;
}

.border-bottom::after {
  content: '';
  position: absolute;
  bottom: -7px;
  left: -9px;

  width: calc(100% + 18px);
  height: 8px;

  background-color: #fff;
}


.border0 {
  border-radius: 25px 0 0 0;
}

.border3 {
  border-radius: 0 0 0 25px;
}

.border4 {
  border-radius: 0 25px 0 0;
}

.border8 {
  border-radius: 0 0 25px 0;
}

.item__background0 {
  background-color: #9AF5D5;
}

.item__background1 {
  background-color: #A2F7EC;
}

.item__background2 {
  background-color: #9CF6E2;
}

.item__background3 {
  background-color: #9CF6E2;
}

.item__background4 {
  background-color: #B4F7FC;
}

.item__background5 {
  background-color: #C0F7FF;
}

.item__background6 {
  background-color: #CDF7FF;
}

.item__background7 {
  background-color: #E4F7FF;
}

.item__background8 {
  background-color: #EEF8FF;
}

.settings--active {
  background-color: #F7F9FC!important;
}
</style>