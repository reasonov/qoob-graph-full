<template>
  <div :class="[
      'graph__item',
      'graph__item' + (index + 1),
      {'current-item': currentItem === index}
      ]"
       :style="itemStyle"
  >
    <div
        :class="['item__main', {'flex': itemParams.height < 160}]"
        :style="textPosition"
        v-show="!settingsActive || index != currentItem"
    >
      <div class="item__header">
        <p class="item__id">0{{ content.id }}</p>
        <img :src="require('../../assets/img/smile' + (content.id - 1) + '.png')" alt="smile-img">
      </div>

      <div :class="['item__body', {'item__body--small': itemParams.height < 160}]" v-show="itemParams.width > 275 || (itemParams.width > 200 && itemParams.height > 160)">
        <p class="item__name">{{ content.name }}</p>

        <div class="item__stat">
          <b class="item__stat--bold">{{ content.number }}</b>
          <p class="item__percent">&nbsp;—&nbsp;{{ content.percent }}%</p>
        </div>

      </div>
    </div>

    <div :class="['about', 'bradius' + index]" v-show="settingsActive && index === currentItem">
      <div class="about__wrapper">

        <arrow-component
            :class="'arrow' + i"
            @changesFromArrow="takeChanges($event, i)"
            :index="i"
            v-for="i in 4"
            :key="i"
        />

        <div class="about__item">
          <p class="about__text">Покупок</p>
          <input
              class="about__data about__data1"
              type="text"
              placeholder="2"
              :value="inputValue('startY')"
              @blur="updateInputValue($event, 'startY')"
          >
          &mdash;
          <input
              class="about__data about__data2"
              type="text"
              placeholder="3"
              :value="inputValue('endY')"
              @blur="updateInputValue($event, 'endY')"
          >
        </div>

        <div class="about__item">
          <p class="about__text">Давность</p>
          <input
              class="about__data about__data1"
              type="text"
              placeholder="0"
              :value="inputValue('startX')"
              @blur="updateInputValue($event, 'startX')"
          >
          &mdash;
          <input
              class="about__data about__data2"
              type="text"
              placeholder="46"
              :value="inputValue('endX')"
              @blur="updateInputValue($event, 'endX')"
          >
        </div>

      </div>
    </div>

    <item-background
        :itemIndex="index"
        :currentItem="currentItem"
        :backgroundIndex="idx"
        :numberOfBackground="itemBackground.length"
        :itemState="itemBackground[idx]"
        :settingsState="settingsActive"
        v-for="(item, idx) in itemBackground"
        :key="item"
    />

  </div>
</template>

<script>
import arrowComponent from "./arrow-component";
import itemBackground from "./item-background";
import { ref, reactive, computed, watch, watchEffect, onMounted } from 'vue';

export default {
  name: "graph-item",
  components: {
    arrowComponent: arrowComponent,
    itemBackground: itemBackground
  },
  props: {
    index: {
      type: Number,
      required: true
    },
    content: {
      type: Object,
      required: true
    },
    settingsActive: {
      type: Boolean,
      required: true
    },
    defaultPosition: {
      type: Object,
      required: true
    },
    oneXWidth: {
      type: Number,
      required: true
    },
    oneYHeight: {
      type: Number,
      required: true
    },
    currentItem: {
      type: Number,
      required: true
    },
    maxX: {
      type: Number,
      required: true
    },
    maxY: {
      type: Number,
      required: true
    },
    modalIsActive: {
      type: Boolean,
      required: false
    }
  },
  setup(props, { emit }) {
    // Параметры блока
    const itemParams = reactive({
      width: 0,
      height: 0,
      x: 0,
      y: 0
    });

    function calcParams() {
      itemParams.width = (props.defaultPosition.endX - props.defaultPosition.startX) * props.oneXWidth + 6;
      itemParams.height = (props.defaultPosition.endY - props.defaultPosition.startY) * props.oneYHeight + 7;
      itemParams.x = (props.defaultPosition.startX * props.oneXWidth) - 8;

      itemParams.y = props.defaultPosition.startY * props.oneYHeight - 8;

    }

    let itemBackground = reactive([]);

    class Background {
      constructor(width = 100, height = 100, position = 0) {
        this.width = width;
        this.height = height;
        this.position = position;
        this.line = 0;
      }
    }

    const diff = computed(() => {
      return props.defaultPosition.endY - props.defaultPosition.startY;
    })

    function numberOfBackground() {
      if(!itemBackground[0]) {
        for(let i = 0; i < diff.value; i++) {
          itemBackground.push(new Background);
          itemBackground[i].line = props.defaultPosition.endY - i;
        }

        if(itemBackground.length === 1) {
          itemBackground[0].height = itemBackground[0].height = props.oneYHeight + 8;
        } else {
          for(let i = 0; i < diff.value; i++) {
            itemBackground[i].height = props.oneYHeight + 4;
          }
        }

      }

      emit('emitBackground', itemBackground);

    }

    watch(() => props.oneYHeight, () => {
      if(props.maxY != 5) {

        for(let i = 0; i <= itemBackground.length + 1; i++) {
          itemBackground.pop();
        }

        for(let i = 0; i < diff.value; i++) {
          itemBackground.push(new Background);
          itemBackground[itemBackground.length - 1].height = props.oneYHeight + 2;
          itemBackground.forEach((item, index, arr) => {
            arr[index].line = props.defaultPosition.endY - index;
          })
        }

        if(itemBackground.length === (props.maxY / 5)) {
          itemBackground[0].height = props.oneYHeight + 6;
        } else {

          for(let i = 0; i < diff.value; i++) {
            itemBackground[i].height = props.oneYHeight + 2;
          }
        }

      }
    })

    function updateBackground(numberOfDel) {
      if(numberOfDel > 0) {
        itemBackground.pop();
        itemBackground.forEach((item, index, arr) => {
          arr[index].line = props.defaultPosition.endY - index;
        })

      } else {
        itemBackground.push(new Background);
        itemBackground[itemBackground.length - 1].height = props.oneYHeight + 4;
        itemBackground.forEach((item, index, arr) => {
          arr[index].line = props.defaultPosition.endY - index;
        })

      }
      if(itemBackground.length === 1) {
        itemBackground[0].height = props.oneYHeight + 6;
      } else {
        for(let i = 0; i < diff.value; i++) {
          itemBackground[i].height = props.oneYHeight + 2;
        }
      }
    }

    setTimeout(() => {
      numberOfBackground();
    }, 200)

    let oldStartY = ref(props.defaultPosition.startY);
    let oldEndY = ref(props.defaultPosition.endY);

    watchEffect(() => {
      if(props.modalIsActive) {
        oldStartY.value = props.defaultPosition.startY;
        oldEndY.value = props.defaultPosition.endY;
      } else {
        if(props.defaultPosition.startY > oldStartY.value || props.defaultPosition.endY < oldEndY.value) {
          updateBackground(1);
        } else if(props.defaultPosition.startY < oldStartY.value || props.defaultPosition.endY > oldEndY.value) {
          updateBackground(0);
        }

        oldStartY.value = props.defaultPosition.startY;
        oldEndY.value = props.defaultPosition.endY;
      }
    })


    // Отправка данных в компонент обертки
    function emitParams(data) {
      emit('updateParams', data)
    }
    // Проверка данных из инпутов и их изменение
    function updateInputValue(event, key) {

      // Ограничение на изменение значений в отрицательное положение
      if(key === 'startX' && props.defaultPosition.startX === 0) {
        emitParams({ [key]: props.defaultPosition[key] + 0.00001, type: 'input' });
        return;

      } else if(key === 'endX' && props.defaultPosition.endX === props.maxX) {
        emitParams({ [key]: props.defaultPosition[key] + 0.00001, type: 'input' });
        return;

      } else if(key === 'startY' && props.defaultPosition.startY === 0) {
        emitParams({ [key]: props.defaultPosition[key] + 0.00001, type: 'input' });
        return;

      } else if(key === 'endY' && props.defaultPosition.endY === props.maxY) {
        emitParams({ [key]: props.defaultPosition[key] + 0.00001, type: 'input' });
        return;

      } else if(key === 'endY' && event.target.value > props.maxY) {
        emitParams({ [key]: props.defaultPosition[key] + 0.00001, type: 'input' });
        return;

      } else if(key === 'endX' && event.target.value > props.maxX) {
        emitParams({ [key]: props.defaultPosition[key] + 0.00001, type: 'input' });
        return;

      }

      // Проверка значений перед отправкой
      if(Number(event.target.value) && Number(event.target.value) >= 0) {
        let value;

        value = Number(event.target.value);
        emitParams({
          value: value,
          type: 'input',
          param: key
        })

      } else {
        emitParams({
          value: props.defaultPosition[key] + 0.00001,
          type: 'input',
          param: key
        })

      }

    }
    // Округление значений для инпута
    function inputValue(key) {

      if(key === 'startY' && props.defaultPosition.startY != 0) {

        return (props.defaultPosition.startY + 1).toFixed(0);

      } else if(key === 'startX' && props.defaultPosition.startX != 0) {

        return (props.defaultPosition.startX + 1).toFixed(0);

      }

      return (props.defaultPosition[key]).toFixed(0);

    }

    watch(props.defaultPosition, () => {

      setTimeout(() => {
        calcParams();
      }, 100)

    }, {deep: true})
    // Перерасчет параметров блока

    // Значение для стрелок
    const arrowY = ref(0);
    const arrowX = ref(0);
    // Получение значений из компонента стрелки
    function takeChanges(data, side) {
      // Проверка на ось стрелки
      if(side === 1 || side === 3) {
        arrowY.value = data;
      } else {
        arrowX.value = data / (props.oneXWidth / 1.5);
      }

      emit('takeArrowY', arrowY.value);
      emit('takeArrowX', arrowX.value);

      if(side === 2) {

        if(props.defaultPosition.endX <= 0 && arrowX.value < 0 || props.defaultPosition.endX === props.maxX) {

          return

        } else {

          const val = ref(props.defaultPosition.endX);
          val.value += arrowX.value;

          emitParams({
            value: val.value,
            param: 'endX',
            side: side,
          })

        }


      } else if(side === 4) {

        if(props.defaultPosition.startX <= 0 && arrowX.value < 0 || props.defaultPosition.startX === 0) {

          return;

        } else {

          const val = ref(props.defaultPosition.startX);
          val.value += arrowX.value;

          emitParams({
            value: val.value,
            param: 'startX',
            side: side
          })

        }

      } else if(side === 1) {

        if(props.defaultPosition.endY <= 0 && arrowY.value < 0 || props.defaultPosition.endY === props.maxY) {

          return

        } else {

          const val = ref(props.defaultPosition.endY);
          val.value += arrowY.value;

          emitParams({
            value: val.value,
            param: 'endY',
            side: side
          })

        }

      } else {

        if(props.defaultPosition.startY <= 0 && arrowY.value < 0 || props.defaultPosition.startY === 0) {

          return

        } else {

          const val = ref(props.defaultPosition.startY);
          val.value += arrowY.value;

          emitParams({
            value: val.value,
            param: 'startY',
            side: side
          })

        }

      }

    }

    const itemStyle = computed(() => {
      if(itemParams.width) {
        return 'width: ' + itemParams.width + 'px; height: ' + itemParams.height + 'px; left: ' + itemParams.x + 'px; bottom: ' + itemParams.y + 'px;'
      }
      return 0;
    })

    const textPosition = computed(() => {

      if(itemBackground[0] && itemBackground[1]) {
        if(itemBackground[0].position > itemBackground[1].position) {
          return 'left: ' + (itemBackground[0].position + 38) + 'px;';
        } else {
          return 'left: ' + (itemBackground[1].position + 38) + 'px;';
        }
      } else if(itemBackground[0]) {
        return 'left: ' + (itemBackground[0].position + 38) + 'px;';
      } else {
        return 0;
      }

    })

    window.addEventListener('resize', () => {
      calcParams();
    })

    onMounted(() => {

      setTimeout(() => {
        calcParams();
      }, 100)

    })

    return {
      itemParams,
      itemStyle,
      itemBackground,
      takeChanges,
      updateInputValue,
      inputValue,
      textPosition
    }
  }
}
</script>

<style scoped>

.flex {
  display: flex;
}

.arrow1 {
  position: absolute;
  top: -2px;
  left: 50%;

  transform: translateX(-50%) rotateZ(180deg);
}

.arrow2 {
  position: absolute;
  top: 50%;
  right: -2px;

  transform: translateY(-50%) rotateZ(-90deg);
}

.arrow3 {
  position: absolute;
  bottom: -2px;
  left: 50%;

  transform: translateX(-50%);
}

.arrow4 {
  position: absolute;
  top: 50%;
  left: -2px;

  transform: translateY(-50%) rotateZ(90deg);
}

.about {
  position: absolute;
  top: 7px;
  left: 7px;
  z-index: 5;

  display: flex;
  justify-content: center;
  align-items: center;

  width: calc(100% - 14px);
  height: calc(100% - 15px);

  border: 2px solid #FED47A;
  box-sizing: border-box;
}

.about__item {
  display: flex;
  align-items: center;

  margin-bottom: 10px;

  font-size: 14px;
}

.about__text {
  width: 64px;

  margin-right: 20px;
}

.about__data {
  display: block;
  width: 30px;
  height: 24px;
  padding: 5px 12px;

  border-radius: 12px;

  font-size: 14px;
  text-align: center;

  background-color: #fff;
}

.about__data1 {
  margin-right: 10px;
}

.about__data2 {
  margin-left: 10px;
}

.graph__item {
  position: absolute;
  cursor: pointer;
}

.graph__item img {
  width: 26px;
  height: 26px;
}

.graph__item1 {
  left: -8px;

  width: 30.6%;
  height: 228px;
}

.graph__item1::before {
  display: none;
}

.graph__item2 {
  left: 29.6%;

  width: 41.7%;
  height: 316px;
}

.graph__item2::before {
  display: none;
}

.graph__item3 {
  left: -8px;

  width: 30.6%;
  height: 206px;
}

.graph__item4 {
  left: -8px;

  width: 30.6%;
  height: 206px;
}

.graph__item4::after {
  display: none;
}

.graph__item5 {
  right: -8px;

  width: 29.6%;
  height: 202px;
}

.graph__item5::before {
  display: none;
}

.graph__item6 {
  right: -8px;

  width: 38.6%;
  height: 225px;
}

.graph__item7 {
  left: 29.6%;

  width: 32.7%;
  height: 316px;
}

.graph__item7::after {
  display: none;
}

.graph__item8 {
  left: 61.9%;

  width: 20%;
  height: 211px;
}

.graph__item8::after {
  display: none;
}

.graph__item9 {
  right: -8px;

  width: 19%;
  height: 210px;
}

.graph__item9::after {
  display: none;
}

.item__main {
  position: absolute;
  top: 38px;
  left: 38px;
  z-index: 5;
}

.current-item {
  z-index: 2;
}

.item__header {
  display: flex;
  align-items: center;
}

.item__body {
  margin-top: 20px;
}

.item__body--small {
  margin-top: 0;
  margin-left: 25px;
}

.item__name {
  font-size: 16px;
}

.item__id {
  display: flex;
  justify-content: center;
  align-items: center;

  width: 28px;
  height: 28px;
  margin-right: 10px;

  border: 2px solid #1E2022;
  border-radius: 10px;

  font-size: 13px;
  font-weight: 600;
  color: #1E2022;
}

.item__stat {
  display: flex;

  margin-top: 10px;

  font-size: 20px;
  font-weight: 300;
}

.item__stat--bold {
  font-weight: 600;
}

.bradius0 {
  border-radius: 12px 0 0 0;
}

.bradius3 {
  border-radius: 0 0 0 12px;
}

.bradius4 {
  border-radius: 0 12px 0 0;
}

.bradius8 {
  border-radius: 0 0 12px 0;
}

</style>