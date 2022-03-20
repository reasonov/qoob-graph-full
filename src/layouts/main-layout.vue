<template>
  <div class="segment">

    <div class="segment__header header">
      <h3 class="segment__title">Сегментация клиентов и построение сетки удержания</h3>

      <ul class="header__list">
        <li class="header__item">
          <p class="header__param">Всего клиентов</p>
          <p class="header__stat">12 180</p>
        </li>
        <li class="header__item">
          <p class="header__param">Средний чек</p>
          <p class="header__stat">1 802 ₽</p>
        </li>
      </ul>

      <div class="header__bottom bottom">

        <p class="bottom__text">Выберите сегмент для получения по нему рекомендаций.</p>

        <div class="settings">
          <button class="settings__axis" v-show="settingsActive" @click="toggleModal">Диапазоны</button>

          <div class="settings__main" @click="settingToggle">
            <p>Режим настройки сегментов</p>
            <toggle-component @settingToggle="settingToggle" :isActive="settingsActive" />
          </div>
        </div>

      </div>
    </div>

    <segment-modal
        v-show="modalIsActive"
        :numberOfX="numberOfX"
        :numberOfY="numberOfY"
        @closeModal="toggleModal"
        @takeModalData="takeModalData"
    />

    <div class="graph-wrapper" ref="graphWrapper">

      <div class="segment__graph" :style="graphStyle">
        <graph-item
            @click="changeCurrentItem(index)"
            @updateParams="takeItemParams($event, index)"
            @takeArrowY="takeArrowY($event)"
            @takeArrowX="takeArrowX($event)"
            @emitBackground="takeBackground($event)"
            v-for="(item, index) in defaultPosition"
            :key="index"
            :index="index"
            :content="contentList[index]"
            :currentItem="currentItem"
            :settingsActive="settingsActive"
            :defaultPosition="item"
            :oneXWidth="oneXWidth"
            :oneYHeight="oneYHeight"
            :maxX="numberOfX.endX"
            :maxY="numberOfY.endY"
            :modalIsActive="modalIsActive"
        />
      </div>

      <div class="graph-axis axis-y">
        <p
            class="graph-axis__item"
            v-for="i in 5"
            :key="i"
        >
          {{ (i * numberOfYAxis).toFixed(0) }}
          <span v-show="i === 5">+</span>
        </p>
      </div>

      <div class="graph-axis axis-x">
        <p
            class="graph-axis__item"
            v-for="i in 9"
            :key="i"
        >
          {{ (i * numberOfXAxis).toFixed(0) }}
        </p>
      </div>

      <span class="graph__zero graph-axis__item">0</span>

    </div>

  </div>
</template>

<script>
import toggleComponent from "../components/graph-components/toggle-component";
import graphItem from "../components/graph-components/graph-item";
import segmentModal from "../components/graph-components/segment-modal";
import itemsContent from "../components/graph-components/data/itemsContent";
import { ref, reactive, computed, watch, onMounted } from 'vue';

export default {
  name: "main-layout",
  components: {
    toggleComponent: toggleComponent,
    graphItem: graphItem,
    segmentModal: segmentModal
  },
  setup(props, { emit }) {

    // Все данные о блоках
    const defaultPosition = reactive([
      {
        endY: 5,
        startY: 3,
        startX: 0,
        endX: 49.6
      },
      {
        endY: 5,
        startY: 3,
        startX: 49.6,
        endX: 100.8
      },
      {
        endY: 3,
        startY: 2,
        startX: 0,
        endX: 49.6
      },
      {
        endY: 2,
        startY: 0,
        startX: 0,
        endX: 49.6
      },
      {
        endY: 5,
        startY: 3,
        startX: 100.8,
        endX: 160
      },
      {
        endY: 3,
        startY: 2,
        startX: 100.8,
        endX: 160
      },
      {
        endY: 3,
        startY: 0,
        startX: 49.6,
        endX: 100.8
      },
      {
        endY: 2,
        startY: 0,
        startX: 100.8,
        endX: 132.8
      },
      {
        endY: 2,
        startY: 0,
        startX: 132.8,
        endX: 160
      }
    ])

    const factorList = reactive([
      {
        factorStartX: 0,
        factorEndX: 0.31,
        factorEndY: 1,
        factorStartY: 0.65
      },
      {
        factorStartX: 0.31,
        factorEndX: 0.72,
        factorEndY: 1,
        factorStartY: 0.496
      },
      {
        factorStartX: 0,
        factorEndX: 0.31,
        factorEndY: 0.638,
        factorStartY: 0.326
      },
      {
        factorStartX: 0,
        factorEndX: 0.31,
        factorEndY: 0.31,
        factorStartY: 0
      },
      {
        factorStartX: 0.72,
        factorEndX: 1,
        factorEndY: 1,
        factorStartY: 0.68
      },
      {
        factorStartX: 0.63,
        factorEndX: 1,
        factorEndY: 0.668,
        factorStartY: 0.35
      },
      {
        factorStartX: 0.31,
        factorEndX: 0.63,
        factorEndY: 0.482,
        factorStartY: 0
      },
      {
        factorStartX: 0.63,
        factorEndX: 0.83,
        factorEndY: 0.338,
        factorStartY: 0
      },
      {
        factorStartX: 0.83,
        factorEndX: 1,
        factorEndY: 0.338,
        factorStartY: 0
      }
    ])

    let backgroundList = reactive([]);

    function takeBackground(data) {

      backgroundList.push(data);

    }

    // Изменение фона для 2 и 7 блока (когда соседи двигаются с двух сторон)
    function backgroundMiddle(index1, index2, index3, pos) {
      let indexForArr;
      let zxc;
      if(backgroundList[index1].length > backgroundList[index2].length) {
        indexForArr = index1;
        zxc = index2;
      } else {
        indexForArr = index2;
        zxc = index1;
      }

      backgroundList[indexForArr].forEach((item) => {
        let currentLine;

        if(backgroundList[zxc].find(ggg => ggg.line === item.line)) {
          currentLine = backgroundList[zxc].find(ggg => ggg.line === item.line).line;
        }

        backgroundList[index3].forEach((item) => {
          if(item.line === currentLine) {
            // console.log(555)
            item.width = (defaultPosition[index2].startX - defaultPosition[index1].endX) * oneXWidth.value + 6;
            if(pos) {
              item.position = (defaultPosition[index1].endX - defaultPosition[index3].startX) * oneXWidth.value;
            }

          }
        })

      })

    }

    function rebuildBackground(index, side) {
      if(index === 0) {
        if(defaultPosition[0].startY > defaultPosition[1].startY) {

          backgroundMiddle(0, 4, 1,true);
          backgroundMiddle(0, 5, 1, true);
          backgroundMiddle(0, 7, 1, true);

        } else if(defaultPosition[0].startY < defaultPosition[6].endY) {

          backgroundMiddle(0, 4, 6,true);
          backgroundMiddle(0, 5, 6, true);
          backgroundMiddle(0, 7, 6, true);

        }

      } else if(index === 1) {

        if(defaultPosition[1].startY > defaultPosition[0].startY && side === 'startX') {
          const diff = defaultPosition[1].endY - defaultPosition[1].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[0][i].width = (defaultPosition[1].startX -  defaultPosition[0].startX) * oneXWidth.value + 7;
            backgroundList[0][i].position = 0;
          }
        }else if(defaultPosition[1].startY > defaultPosition[4].startY && side === 'endX') {
          const diff = defaultPosition[1].endY - defaultPosition[1].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[4][i].width = (defaultPosition[4].endX -  defaultPosition[1].endX) * oneXWidth.value + 7;
            backgroundList[4][i].position = (defaultPosition[1].endX - defaultPosition[4].startX) * oneXWidth.value;
          }
        } else if(defaultPosition[2].startY < defaultPosition[1].startY && defaultPosition[2].endY > defaultPosition[1].startY && side === 'startX') {
          const diff = defaultPosition[2].endY - defaultPosition[1].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[2][i].width = (defaultPosition[1].startX -  defaultPosition[2].startX) * oneXWidth.value + 7;
            backgroundList[2][i].position = 0;
          }
        } else if(defaultPosition[5].startY < defaultPosition[1].startY && defaultPosition[5].endY > defaultPosition[1].startY && side === 'endX') {
          const diff = defaultPosition[1].startY - defaultPosition[5].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[5][i].width = (defaultPosition[5].endX -  defaultPosition[1].endX) * oneXWidth.value + 7;
            backgroundList[5][i].position = (defaultPosition[1].endX - defaultPosition[5].startX) * oneXWidth.value;
          }
        } else if(defaultPosition[3].endY > defaultPosition[1].startY && side === 'startX') {
          const diff = defaultPosition[1].startY - defaultPosition[3].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[3][i].width = (defaultPosition[1].startX -  defaultPosition[3].startX) * oneXWidth.value + 7;
            backgroundList[3][i].position = 0;
          }
        } else if(defaultPosition[7].endY > defaultPosition[1].startY && side === 'endX') {
          const diff = defaultPosition[7].endY - defaultPosition[1].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[7][i].width = (defaultPosition[7].endX -  defaultPosition[1].endX) * oneXWidth.value + 7;
            backgroundList[7][i].position = (defaultPosition[1].endX - defaultPosition[7].startX) * oneXWidth.value;
          }
        } else {
          for(let i = 0; i < backgroundList[1].length; i++) {
            backgroundList[1][i].width = 100;
            backgroundList[1][i].position = 0;
          }
        }

      } else if(index === 2) {

        if(defaultPosition[2].endY > defaultPosition[1].startY) {

          backgroundMiddle(2, 4, 1,true);
          backgroundMiddle(2, 5, 1, true);
          backgroundMiddle(2, 7, 1, true);

        }
        if(defaultPosition[2].startY < defaultPosition[6].endY && side === 'endX') {
          console.log('444')
          backgroundMiddle(2, 4, 6,true);
          backgroundMiddle(2, 5, 6, true);
          backgroundMiddle(2, 7, 6, true);

        }

      } else if(index === 3) {

        if(defaultPosition[3].endY > defaultPosition[1].startY) {

          backgroundMiddle(3, 4, 1,true);
          backgroundMiddle(3, 5, 1, true);
          backgroundMiddle(3, 7, 1, true);

        } else if(defaultPosition[3].endY < defaultPosition[6].endY) {

          backgroundMiddle(3, 4, 6,true);
          backgroundMiddle(3, 5, 6, true);
          backgroundMiddle(3, 7, 6, true);

        }

      } else if(index === 4) {
        if(defaultPosition[4].startY > defaultPosition[1].startY) {
          backgroundMiddle(0, 4, 1);
          backgroundMiddle(2, 4, 1);
          backgroundMiddle(3, 4, 1);

        } else if(defaultPosition[4].startY < defaultPosition[6].endY) {

          backgroundMiddle(0, 4, 6);
          backgroundMiddle(2, 4, 6);
          backgroundMiddle(3, 4, 6);

        }

      } else if(index === 5) {

        if(defaultPosition[5].endY > defaultPosition[1].startY && side === 'startX') {

          backgroundMiddle(0, 5, 1);
          backgroundMiddle(2, 5, 1);
          backgroundMiddle(3, 5, 1);


        }
        if(defaultPosition[5].startY < defaultPosition[6].endY && side === 'startX') {

          backgroundMiddle(0, 5, 6);
          backgroundMiddle(2, 5, 6);
          backgroundMiddle(3, 5, 6);

        }

      } else if(index === 6) {

        if(defaultPosition[6].endY > defaultPosition[0].startY && side === 'startX') {
          const diff = defaultPosition[6].endY - defaultPosition[0].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[0][backgroundList[0].length - i - 1].width = (defaultPosition[6].startX -  defaultPosition[0].startX) * oneXWidth.value + 7;
            backgroundList[0][backgroundList[0].length - i - 1].position = 0;
          }
        } else if(defaultPosition[6].endY > defaultPosition[4].startY && side === 'endX') {
          const diff = defaultPosition[6].endY - defaultPosition[4].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[4][backgroundList[4].length - i - 1].width = (defaultPosition[4].endX -  defaultPosition[6].endX) * oneXWidth.value + 7;
            backgroundList[4][backgroundList[4].length - i - 1].position = (defaultPosition[6].endX - defaultPosition[4].startX) * oneXWidth.value;
          }
        } else if(defaultPosition[2].endY > defaultPosition[6].endY && defaultPosition[2].startY < defaultPosition[6].endY && side === 'startX') {
          const diff = defaultPosition[2].endY - defaultPosition[6].endY;

          for(let i = 0; i < diff; i++) {
            backgroundList[2][backgroundList[2].length - i - 1].width = (defaultPosition[6].startX -  defaultPosition[2].startX) * oneXWidth.value + 7;
            backgroundList[2][backgroundList[2].length - i - 1].position = 0;
          }
        } else if(defaultPosition[5].endY > defaultPosition[6].endY && defaultPosition[6].endY > defaultPosition[5].startY && side === 'endX') {
          const diff = defaultPosition[5].endY - defaultPosition[6].endY;
          for(let i = 0; i < diff; i++) {
            backgroundList[5][backgroundList[5].length - i - 1].width = (defaultPosition[5].endX -  defaultPosition[6].endX) * oneXWidth.value + 7;
            backgroundList[5][backgroundList[5].length - i - 1].position = (defaultPosition[6].endX - defaultPosition[5].startX) * oneXWidth.value;
          }
        } else if(defaultPosition[3].endY > defaultPosition[6].endY && side === 'startX') {
          const diff = defaultPosition[3].endY - defaultPosition[6].endY;
          for(let i = 0; i < diff; i++) {
            backgroundList[3][backgroundList[3].length - i - 1].width = (defaultPosition[6].startX -  defaultPosition[3].startX) * oneXWidth.value + 7;
            backgroundList[3][backgroundList[3].length - i - 1].position = 0;
          }
        } else if(defaultPosition[7].endY > defaultPosition[6].endY && side === 'endX') {
          const diff = defaultPosition[7].endY - defaultPosition[6].endY;
          for(let i = 0; i < diff; i++) {
            backgroundList[7][backgroundList[7].length - i - 1].width = (defaultPosition[7].endX -  defaultPosition[6].endX) * oneXWidth.value + 7;
            backgroundList[7][backgroundList[7].length - i - 1].position = (defaultPosition[6].endX - defaultPosition[7].startX) * oneXWidth.value;
          }
        } else {
          for(let i = 0; i < backgroundList[6].length; i++) {
            backgroundList[6][i].width = 100;
            backgroundList[6][i].position = 0;
          }
        }

        if(side === 'endY') {
          for(let i = 0; i < backgroundList[1].length; i++) {
            backgroundList[1][i].width = 100;
            backgroundList[1][i].position = 0;
          }
        }

        if(defaultPosition[6].endX > defaultPosition[4].startX) {
          const diff = defaultPosition[6].endY - defaultPosition[4].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[4][backgroundList[4].length - i - 1].width = (defaultPosition[4].endX - defaultPosition[6].endX) * oneXWidth.value + 7;
            backgroundList[4][backgroundList[4].length - i - 1].position = (defaultPosition[6].endX - defaultPosition[4].startX) * oneXWidth.value;
          }
        }

        if(defaultPosition[6].startX < defaultPosition[0].endX) {
          const diff = defaultPosition[6].endY - defaultPosition[0].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[0][backgroundList[4].length - i - 1].width = (defaultPosition[6].startX - defaultPosition[0].startX) * oneXWidth.value + 7;
          }
        }

      }
      else if(index === 7) {

        if(defaultPosition[7].endY > defaultPosition[1].startY) {

          backgroundMiddle(0, 7, 1);
          backgroundMiddle(2, 7, 1);
          backgroundMiddle(3, 7, 1);

        } else if(defaultPosition[7].endY < defaultPosition[6].endY) {

          backgroundMiddle(0, 7, 6);
          backgroundMiddle(2, 7, 6);
          backgroundMiddle(3, 7, 6);

        }

      }

      if(side === 'startY') {
        for (let i = 0; i < backgroundList[6].length; i++) {
          backgroundList[6][i].width = 100;
          backgroundList[6][i].position = 0;
        }
      }

    }

    function normalizeBlock(index1, index2, index3, index4, side) {
      if(side === 'endX') {
        if(defaultPosition[index3].endY > defaultPosition[index4].startY && defaultPosition[index1].endX >= defaultPosition[index4].startX  && defaultPosition[index2].endX >= defaultPosition[index4].startX && defaultPosition[index3].endX >= defaultPosition[index4].startX) {
          const min = Math.min(defaultPosition[index1].endX, defaultPosition[index2].endX, defaultPosition[index3].endX)
          defaultPosition[index4].startX = min;
          rebuildBackground(index1, 'endX');
          rebuildBackground(index2, 'endX');
          rebuildBackground(index3, 'endX');
        } else if(defaultPosition[index2].endY > defaultPosition[index4].startY && defaultPosition[index1].endX >= defaultPosition[index4].startX  && defaultPosition[index2].endX >= defaultPosition[index4].startX) {
          const min = Math.min(defaultPosition[index1].endX, defaultPosition[index2].endX)
          defaultPosition[index4].startX = min;
          rebuildBackground(index1, 'endX');
          rebuildBackground(index2, 'endX');
        }
      } else {
        if(defaultPosition[index3].endY > defaultPosition[index4].startY && defaultPosition[index1].startX <= defaultPosition[index4].endX && defaultPosition[index2].startX <= defaultPosition[index4].endX && defaultPosition[index3].startX <= defaultPosition[index4].endX) {
          const min = Math.max(defaultPosition[index1].startX, defaultPosition[index2].startX, defaultPosition[index3].startX)
          defaultPosition[index4].endX = min;
          rebuildBackground(index1, 'startX');
          rebuildBackground(index2, 'startX');
          rebuildBackground(index3, 'startX');
        } else if(defaultPosition[index2].endY > defaultPosition[index4].startY && defaultPosition[index1].startX <= defaultPosition[index4].endX && defaultPosition[index2].startX <= defaultPosition[index4].endX) {
          const min = Math.max(defaultPosition[index1].startX, defaultPosition[index2].startX)
          defaultPosition[index4].endX = min;
          rebuildBackground(index1, 'startX');
          rebuildBackground(index2, 'startX');
        }
      }
    }

    function normalizeBlock2(index1, index2, index3, index4, side) {
      if(side === 'endX') {
        if(defaultPosition[index1].startY < defaultPosition[index4].endY && defaultPosition[index1].endX >= defaultPosition[index4].startX  && defaultPosition[index2].endX >= defaultPosition[index4].startX  && defaultPosition[index3].endX >= defaultPosition[index4].startX) {
          const min = Math.min(defaultPosition[index1].endX, defaultPosition[index2].endX, defaultPosition[index3].endX)
          defaultPosition[index4].startX = min;
          rebuildBackground(index1, 'endX');
          rebuildBackground(index2, 'endX');
          rebuildBackground(index3, 'endX');
        } else if(defaultPosition[index2].startY < defaultPosition[index4].endY && defaultPosition[index2].endX > defaultPosition[index4].startX  && defaultPosition[index3].endX > defaultPosition[index4].startX) {
          const min = Math.min(defaultPosition[index2].endX, defaultPosition[index3].endX)
          defaultPosition[index4].startX = min;
          rebuildBackground(index2, 'endX');
          rebuildBackground(index3, 'endX');
        }
      } else {
        if(defaultPosition[index1].startY < defaultPosition[index4].endY && defaultPosition[index1].startX <= defaultPosition[index4].endX && defaultPosition[index2].startX <= defaultPosition[index4].endX && defaultPosition[index3].startX <= defaultPosition[index4].endX) {
          const min = Math.max(defaultPosition[index1].startX, defaultPosition[index2].startX, defaultPosition[index3].startX)
          defaultPosition[index4].endX = min;
          rebuildBackground(index1, 'startX');
          rebuildBackground(index2, 'startX');
          rebuildBackground(index3, 'startX');
        } else if(defaultPosition[index2].startY < defaultPosition[index4].endY && defaultPosition[index2].startX <= defaultPosition[index4].endX && defaultPosition[index3].startX <= defaultPosition[index4].endX) {
          const min = Math.max(defaultPosition[index2].startX, defaultPosition[index3].startX)
          defaultPosition[index4].endX = min;
          rebuildBackground(index2, 'startX');
          rebuildBackground(index3, 'startX');
        }
      }
    }

    // Функция для перерасчета значений блоков
    function rebuildBlock(index, param) {
      if(index === 0) {

        if(defaultPosition[0].endX >= defaultPosition[2].endX) {
          defaultPosition[2].endY = defaultPosition[0].startY;
        } else if(param === 'startY') {
          const diff = defaultPosition[2].endY - defaultPosition[0].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[2][i].width = (defaultPosition[2].endX - defaultPosition[0].endX) * oneXWidth.value + 7;
            backgroundList[2][i].position = defaultPosition[0].endX * oneXWidth.value;
          }
        }

        if(param === 'startX' || param === 'endX') {

          if(defaultPosition[0].startY <= defaultPosition[1].startY) {

            defaultPosition[1].startX = defaultPosition[0].endX;

          }
        }

        if(param === 'endX') {
          normalizeBlock(0, 2, 3, 1, 'endX');
          normalizeBlock2(0, 2, 3, 6, 'endX');
        }

      } else if(index === 1) {

        defaultPosition[6].endY = defaultPosition[index].startY;

        if(defaultPosition[1].startY <= defaultPosition[0].startY) {
          defaultPosition[0].endX = defaultPosition[1].startX;
        }

        if(defaultPosition[1].startY <= defaultPosition[4].startY) {
          defaultPosition[4].startX = defaultPosition[1].endX;
        }

        if(defaultPosition[1].startY <= defaultPosition[2].startY) {
          defaultPosition[2].endX = defaultPosition[1].startX;
        }

        if(defaultPosition[1].startY <= defaultPosition[5].startY) {
          defaultPosition[5].startX = defaultPosition[1].endX;
        }

      } else if(index === 2) {

        defaultPosition[3].endY = defaultPosition[index].startY;
        defaultPosition[0].startY = defaultPosition[index].endY;

        if(param === 'endX') {
          normalizeBlock(0, 2, 3, 1, 'endX');
          normalizeBlock2(0, 2, 3, 6, 'endX');
        }

      } else if(index === 3) {

        defaultPosition[2].startY = defaultPosition[3].endY;

        if(defaultPosition[3].endY >= defaultPosition[6].endY) {

          defaultPosition[6].startX = defaultPosition[3].endX;

        }

        if(param === 'endX') {
          normalizeBlock(0, 2, 3, 1, 'endX');
          normalizeBlock2(0, 2, 3, 6, 'endX');
        }

      } else if(index === 4) {

        defaultPosition[5].endY = defaultPosition[index].startY;

        if(defaultPosition[4].startY <= defaultPosition[1].startY) {

          defaultPosition[1].endX = defaultPosition[4].startX;

        }

        if(param === 'startX') {
          normalizeBlock(4, 5, 7, 1);
          normalizeBlock2(4, 5, 7, 6);
        }

      } else if(index === 5) {

        defaultPosition[4].startY = defaultPosition[index].endY;
        defaultPosition[7].endY = defaultPosition[index].startY;
        defaultPosition[8].endY = defaultPosition[index].startY;

        if(param === 'startX') {
          normalizeBlock(4, 5, 7, 1);
          normalizeBlock2(4, 5, 7, 6);
        }

      } else if(index === 6) {

        defaultPosition[1].startY = defaultPosition[index].endY;

        if(defaultPosition[6].endY >= defaultPosition[7].endY) {
          defaultPosition[7].startX = defaultPosition[6].endX;
        }
        if(defaultPosition[6].endY >= defaultPosition[3].endY) {
          defaultPosition[3].endX = defaultPosition[6].startX;
        }

        if(defaultPosition[6].endY >= defaultPosition[2].endY) {
          defaultPosition[2].endX = defaultPosition[6].startX;
        }

        if(defaultPosition[6].endY >= defaultPosition[5].endY) {
          defaultPosition[5].startX = defaultPosition[6].endX;
        }

      } else if(index === 7) {

        defaultPosition[8].endY = defaultPosition[index].endY;
        defaultPosition[5].startY = defaultPosition[index].endY;

        defaultPosition[8].startX = defaultPosition[7].endX;

        if(defaultPosition[7].endY >= defaultPosition[6].endY) {

          defaultPosition[6].endX = defaultPosition[7].startX;

        }

        if(param === 'startX') {
          normalizeBlock(4, 5, 7, 1);
          normalizeBlock2(4, 5, 7, 6);
        }

      } else if(index === 8) {

        defaultPosition[7].endY = defaultPosition[index].endY;
        defaultPosition[5].startY = defaultPosition[index].endY;

        defaultPosition[7].endX = defaultPosition[8].startX;

      }

    }
    // Значение возращаемое со стрелочек
    const arrowX = ref(0);
    const arrowY = ref(0);

    function takeArrowX(data) {
      arrowX.value = data;
    }

    function takeArrowY(data) {
      arrowY.value = data;
    }

    // Объект лимитов
    const isLimit = reactive({
      index: null,
      startX: false,
      endX: false,
      startY: false,
      endY: false
    });

    // Перерасчет объекта с лимитами
    function addNewLimit(newIndex, newKey, state) {

      if(isLimit.index != newIndex) {
        for(let key in isLimit) {
          isLimit[key] = false;
        }
      }

      isLimit.index = newIndex;
      isLimit[newKey] = state;

    }

    // Проверка на достижение лимитов (Полное перекрытие соседнего блока)
    function checkLimit(index, side) {

      if(defaultPosition[index].endY - defaultPosition[index].startY <= 1) {

        if(defaultPosition[index].endY === numberOfY.endY) {

          if(arrowY.value > 0) {
            addNewLimit(index, 'startY', true);
          } else {
            addNewLimit(index, 'startY', false);
          }

        } else if(defaultPosition[index].startY === 0) {

          if(arrowY.value < 0) {
            addNewLimit(index, 'endY', true);
          } else {
            addNewLimit(index, 'endY', false);
          }

        }
        if(side === 1 && arrowY.value < 0) {
          addNewLimit(index, 'endY', true);
        } else if(side === 3 && arrowY.value > 0) {
          addNewLimit(index, 'startY', true);
        } else {
          addNewLimit(index, 'endY', false);
          addNewLimit(index, 'startY', false);
        }

      }

      if(defaultPosition[index].endX - defaultPosition[index].startX <= 15) {
        if(side === 2) {
          if(arrowX.value < 0) {
            addNewLimit(index, 'endX', true);
          } else {
            addNewLimit(index, 'endX', false);
          }
        } else if(side === 4) {
          if(arrowX.value > 0) {
            addNewLimit(index, 'startX', true);
          } else {
            addNewLimit(index, 'startX', false);
          }
        }
      }

      if(index === 0) {
        if(defaultPosition[0].endX >= defaultPosition[1].endX - 2) {

          if(arrowX.value > 0) {

            addNewLimit(index, 'endX', true);

          } else {

            addNewLimit(index, 'endX', false);

          }

        }
        if(defaultPosition[0].startY <= defaultPosition[2].startY + 1) {

          if(arrowY.value < 0) {

            addNewLimit(index, 'startY', true);

          } else {

            addNewLimit(index, 'startY', false);

          }

        }

      } else if(index === 1) {
        if(defaultPosition[1].startX <= 2) {

          if(arrowX.value < 0) {

            addNewLimit(index, 'startY', true);

          } else {

            addNewLimit(index, 'startY', false);

          }

        }
        if(defaultPosition[1].startY <= 1) {

          if(arrowY.value < 0) {

            addNewLimit(index, 'startY', true);

          } else {

            addNewLimit(index, 'startY', false);

          }

        }
        if(defaultPosition[1].endX >= defaultPosition[4].endX - 2) {

          if(arrowX.value > 0) {

            addNewLimit(index, 'endX', true);

          } else {

            addNewLimit(index, 'endX', false);

          }

        }

      } else if(index === 2) {
        if(defaultPosition[2].startY <= 1) {

          if(arrowY.value < 0) {

            addNewLimit(index, 'startY', true);

          } else {

            addNewLimit(index, 'startY', false);

          }

        }
        if(defaultPosition[2].endY >= defaultPosition[0].endY - 1) {

          if(arrowY.value > 0) {

            addNewLimit(index, 'endY', true);

          } else {

            addNewLimit(index, 'endY', false);

          }

        }
        if(defaultPosition[2].endX >= defaultPosition[6].endX - 2) {

          if(arrowX.value > 0) {

            addNewLimit(index, 'endX', true);

          } else {

            addNewLimit(index, 'endX', false);

          }

        }

      } else if(index === 3) {

        if(defaultPosition[3].endY >= defaultPosition[2].endY - 1) {

          if(arrowY.value > 0) {

            addNewLimit(index, 'endY', true);

          } else {

            addNewLimit(index, 'endY', false);

          }

        }
        if(defaultPosition[3].endX >= defaultPosition[6].endX - 2) {

          if(arrowX.value > 0) {

            addNewLimit(index, 'endX', true);

          } else {

            addNewLimit(index, 'endX', false);

          }

        }

      } else if(index === 4) {

        if(defaultPosition[4].startX <= defaultPosition[1].startX - 2) {

          if(arrowX.value < 0) {

            addNewLimit(index, 'startX', true);

          } else {

            addNewLimit(index, 'startX', false);

          }

        }
        if(defaultPosition[4].startY <= defaultPosition[5].startY + 1) {

          if(arrowY.value < 0) {

            addNewLimit(index, 'startY', true);

          } else {

            addNewLimit(index, 'startY', false);

          }

        }

      } else if(index === 5) {
        if(defaultPosition[5].endY >= defaultPosition[4].endY - 1) {

          if(arrowY.value > 0) {

            addNewLimit(index, 'endY', true);

          } else {

            addNewLimit(index, 'endY', false);

          }

        }
        if(defaultPosition[5].startX <= defaultPosition[1].startX - 2) {
          if(arrowX.value < 0) {

            addNewLimit(index, 'startX', true);

          } else {

            addNewLimit(index, 'startX', false);

          }

        }
        if(defaultPosition[5].startY <= 1) {
          if(arrowY.value < 0) {

            addNewLimit(index, 'startY', true);

          } else {

            addNewLimit(index, 'startY', false);

          }

        }
      } else if(index === 6) {
        if(defaultPosition[6].endY >= defaultPosition[1].endY - 1) {

          if(arrowY.value > 0) {

            addNewLimit(index, 'endY', true);

          } else {

            addNewLimit(index, 'endY', false);

          }

        }
        if(defaultPosition[6].startX <= 2) {

          if(arrowX.value < 0) {

            addNewLimit(index, 'startX', true);

          } else {

            addNewLimit(index, 'startX', false);

          }

        }
        if(defaultPosition[6].endX >= defaultPosition[7].endX - 2) {

          if(arrowX.value > 0) {

            addNewLimit(index, 'endX', true);

          } else {

            addNewLimit(index, 'endX', false);

          }

        }

      } else if(index === 7) {
        if(defaultPosition[7].startX <= defaultPosition[6].startX - 2) {

          if(arrowX.value < 0) {

            addNewLimit(index, 'startX', true);

          } else {

            addNewLimit(index, 'startX', false);

          }

        }
        if(defaultPosition[7].endY >= defaultPosition[5].endY - 1) {

          if(arrowY.value > 0) {

            addNewLimit(index, 'endY', true);

          } else {

            addNewLimit(index, 'endY', false);

          }

        }
        if(defaultPosition[7].endX >= defaultPosition[8].endX - 2) {

          if(arrowX.value > 0) {

            addNewLimit(index, 'endX', true);

          } else {

            addNewLimit(index, 'endX', false);

          }

        }

      } else if(index === 8) {
        if(defaultPosition[8].startX <= defaultPosition[7].startX - 2) {

          if(arrowX.value < 0) {

            addNewLimit(index, 'startX', true);

          } else {

            addNewLimit(index, 'startX', false);

          }

        }
        if(defaultPosition[8].endY >= defaultPosition[5].endY - 1) {

          if(arrowY.value > 0) {

            addNewLimit(index, 'endY', true);

          } else {

            addNewLimit(index, 'endY', false);

          }

        }

      }

    }

    function limitForInput(index, side) {
      if(defaultPosition[index].endY === defaultPosition[index].startY) {
        if(side === 'startY') {
          defaultPosition[index].startY -= 1;
        } else if(side === 'endY') {
          defaultPosition[index].endY += 1;
        }
      }

      if(index === 0) {

        if(defaultPosition[0].endX >= defaultPosition[1].endX - 2) {
          defaultPosition[0].endX = defaultPosition[1].endX - 2;
        }
        if(defaultPosition[0].startY <= defaultPosition[2].startY + 1) {
          defaultPosition[0].startY = defaultPosition[2].startY + 1;
        }
      } else if(index === 1) {
        if(defaultPosition[1].startX <= 2) {
          defaultPosition[1].startX = 2
        }
        if(defaultPosition[1].startY <= 1) {
          defaultPosition[1].startY = 1
        }
        if(defaultPosition[1].endX >= defaultPosition[4].endX - 2) {
          defaultPosition[1].endX = defaultPosition[4].endX - 2
        }
      } else if(index === 2) {
        if(defaultPosition[2].startY <= 1) {
          defaultPosition[2].startY = 1
        }
        if(defaultPosition[2].endY >= defaultPosition[0].endY) {
          defaultPosition[2].endY = defaultPosition[0].endY - 2
        }
        if(defaultPosition[2].endX >= defaultPosition[6].endX - 2) {
          defaultPosition[2].endX = defaultPosition[6].endX - 2
        }
      } else if(index === 3) {
        if(defaultPosition[3].endY >= defaultPosition[2].endY - 1) {
          defaultPosition[3].endY = defaultPosition[2].endY - 1
        }
        if(defaultPosition[3].endX >= defaultPosition[6].endX - 2) {
          defaultPosition[3].endX = defaultPosition[6].endX - 2
        }
      } else if(index === 4) {
        if(defaultPosition[4].startX <= defaultPosition[1].startX + 2) {
          defaultPosition[4].startX = defaultPosition[1].startX + 2
        }
        if(defaultPosition[4].startY <= defaultPosition[5].startY + 1) {
          defaultPosition[4].startY = defaultPosition[5].startY + 1
        }
      } else if(index === 5) {
        if(defaultPosition[5].endY >= defaultPosition[4].endY - 1) {
          defaultPosition[5].endY = defaultPosition[4].endY - 1
        }
        if(defaultPosition[5].startX <= defaultPosition[1].startX + 2) {
          defaultPosition[5].startX = defaultPosition[1].startX + 2
        }
        if(defaultPosition[5].startY <= 1) {
          defaultPosition[5].startY = 1;
        }
      } else if(index === 6) {
        if(defaultPosition[6].endY >= defaultPosition[1].endY - 1) {
          defaultPosition[6].endY = defaultPosition[1].endY - 1
        }
        if(defaultPosition[6].startX <= 2) {
          defaultPosition[6].startX = 2
        }
        if(defaultPosition[6].endX >= defaultPosition[7].endX - 2) {
          defaultPosition[6].endX = defaultPosition[7].endX - 2
        }
      } else if(index === 7) {
        if(defaultPosition[7].startX <= defaultPosition[6].startX + 2) {
          defaultPosition[7].startX = defaultPosition[6].startX + 2
        }
        if(defaultPosition[7].endY >= defaultPosition[5].endY - 1) {
          defaultPosition[7].endY = defaultPosition[5].endY - 1
        }
        if(defaultPosition[7].endX >= defaultPosition[8].endX - 2) {
          defaultPosition[7].endX = defaultPosition[8].endX - 2
        }
      } else if(index === 8) {
        if(defaultPosition[8].startX <= defaultPosition[7].startX + 2) {
          defaultPosition[8].startX = defaultPosition[7].startX + 2
        }
        if(defaultPosition[8].endY >= defaultPosition[5].endY - 1) {
          defaultPosition[8].endY = defaultPosition[5].endY - 1
        }
      }

    }

    // Принимает данные об изменении из дочерних блоков
    function takeItemParams(data, index) {

      checkLimit(index, data.side, data.param);
      // Проверка на отсутствие лимитов
      if(isLimit.index === index && isLimit[data.param]) {
        return;

      } else {

        defaultPosition[index][data.param] = data.value;

        if(data.type === 'input') {
          limitForInput(index, data.param);
        }

        // Перестройка блока по высоте в зависимости от соседей
        rebuildBlock(index, data.param);
        rebuildBackground(index, data.param);
        updateFactor();
      }

    }

    // Список контента для блоков
    const contentList = itemsContent();

    // SETTINGS

    const settingsActive = ref(false);
    // Переключение состояния режима настроек
    function settingToggle() {
      settingsActive.value = !settingsActive.value;
    }

    // SETTINGS

    // CURRENT ITEM

    function rebuildForCurrentItem(index) {

      backgroundList[index].forEach((item) => {
        item.width = 100;
        item.position = 0;
      })

      if(index === 0) {

        if(defaultPosition[6].endY > defaultPosition[0].startY && defaultPosition[6].startX < defaultPosition[0].endX) {
          const diff = defaultPosition[6].endY - defaultPosition[0].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[6][i].width = (defaultPosition[6].endX -  defaultPosition[0].endX) * oneXWidth.value + 7;
            backgroundList[6][i].position = (defaultPosition[0].endX - defaultPosition[6].startX) * oneXWidth.value;
          }
        }

        if(defaultPosition[1].endY > defaultPosition[0].startY && defaultPosition[1].startX < defaultPosition[0].endX) {
          const diff = defaultPosition[1].endY - defaultPosition[0].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[1][i].width = (defaultPosition[1].endX -  defaultPosition[0].endX) * oneXWidth.value + 7;
            backgroundList[1][i].position = (defaultPosition[0].endX - defaultPosition[1].startX) * oneXWidth.value;
          }
        }

      } else if(index === 1) {

        if(defaultPosition[2].endX > defaultPosition[1].startX && defaultPosition[1].startY < defaultPosition[2].endY) {

          const diff = defaultPosition[2].endY - defaultPosition[1].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[2][i].width = (defaultPosition[1].startX - defaultPosition[2].startX) * oneXWidth.value + 7;
            backgroundList[2][i].position = 0;
          }

        }

        if(defaultPosition[1].endX > defaultPosition[5].startX && defaultPosition[1].startY < defaultPosition[5].endY) {

          const diff = defaultPosition[5].endY - defaultPosition[1].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[5][i].width = (defaultPosition[5].endX - defaultPosition[1].endX) * oneXWidth.value + 7;
            backgroundList[5][i].position = (defaultPosition[1].endX - defaultPosition[5].startX) * oneXWidth.value;
          }

        }

        if(defaultPosition[3].endX > defaultPosition[1].startX && defaultPosition[1].startY < defaultPosition[3].endY) {

          const diff = defaultPosition[3].endY - defaultPosition[1].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[3][i].width = (defaultPosition[1].startX - defaultPosition[3].startX) * oneXWidth.value + 7;
            backgroundList[3][i].position = 0;
          }

        }

        if(defaultPosition[1].endX > defaultPosition[7].startX && defaultPosition[1].startY < defaultPosition[7].endY) {

          const diff = defaultPosition[7].endY - defaultPosition[1].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[7][i].width = (defaultPosition[7].endX - defaultPosition[1].endX) * oneXWidth.value + 7;
            backgroundList[7][i].position = (defaultPosition[1].endX - defaultPosition[7].startX) * oneXWidth.value;
          }

        }

        if(defaultPosition[0].startY > defaultPosition[1].startY && defaultPosition[0].endX > defaultPosition[1].startX) {

          const diff = defaultPosition[0].endY - defaultPosition[0].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[0][i].width = (defaultPosition[1].startX - defaultPosition[0].startX) * oneXWidth.value + 7;
            backgroundList[0][i].position = 0;
          }

        }

        if(defaultPosition[4].startY > defaultPosition[1].startY && defaultPosition[4].startX < defaultPosition[1].endX) {

          const diff = defaultPosition[4].endY - defaultPosition[4].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[0][i].width = (defaultPosition[4].endX - defaultPosition[1].endX) * oneXWidth.value + 7;
            backgroundList[0][i].position = (defaultPosition[1].endX - defaultPosition[4].startX) * oneXWidth.value;
          }

        }



      } else if(index === 2) {

        if(defaultPosition[2].endY > defaultPosition[1].startY && defaultPosition[2].startY < defaultPosition[1].startY && defaultPosition[1].startX < defaultPosition[3].endX) {
          const diff = defaultPosition[2].endY - defaultPosition[1].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[1][backgroundList[1].length - i - 1].width = (defaultPosition[1].endX - defaultPosition[2].endX) * oneXWidth.value + 7;
            backgroundList[1][backgroundList[1].length - i - 1].position = (defaultPosition[2].endX - defaultPosition[1].startX) * oneXWidth.value;
          }

        }

        if(defaultPosition[2].endY > defaultPosition[6].endY && defaultPosition[2].startY < defaultPosition[6].endY && defaultPosition[6].startX < defaultPosition[2].endX) {
          const diff = defaultPosition[6].endY - defaultPosition[2].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[6][i].width = (defaultPosition[6].endX - defaultPosition[2].endX) * oneXWidth.value + 7;
            backgroundList[6][i].position = (defaultPosition[2].endX - defaultPosition[6].startX) * oneXWidth.value;
          }

        }

      } else if(index === 3) {

        if(defaultPosition[1].startY < defaultPosition[3].endY && defaultPosition[1].startX < defaultPosition[3].endX) {
          const diff = defaultPosition[3].endY - defaultPosition[1].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[1][backgroundList[1].length - i - 1].width = (defaultPosition[1].endX - defaultPosition[3].endX) * oneXWidth.value + 7;
            backgroundList[1][backgroundList[1].length - i - 1].position = (defaultPosition[3].endX - defaultPosition[1].startX) * oneXWidth.value;
          }

        }

        if(defaultPosition[6].endY < defaultPosition[3].endY && defaultPosition[6].startX < defaultPosition[3].endX) {

          const diff = defaultPosition[6].endY - defaultPosition[6].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[6][backgroundList[6].length - i - 1].width = (defaultPosition[6].endX - defaultPosition[3].endX) * oneXWidth.value + 7;
            backgroundList[6][backgroundList[6].length - i - 1].position = (defaultPosition[3].endX - defaultPosition[6].startX) * oneXWidth.value;
          }

        }

      } else if(index === 4) {

        if(defaultPosition[6].endY > defaultPosition[4].startY && defaultPosition[6].endX > defaultPosition[4].startX) {

          const diff = defaultPosition[6].endY - defaultPosition[4].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[6][i].width = (defaultPosition[4].startX - defaultPosition[6].startX) * oneXWidth.value + 7;
            backgroundList[6][i].position = 0;
          }
        }
        if(defaultPosition[1].endX > defaultPosition[4].startX) {
          defaultPosition[1].endX = defaultPosition[4].startX;
        }

      } else if(index == 5) {

        if(defaultPosition[5].endY > defaultPosition[1].startY && defaultPosition[5].startY < defaultPosition[1].startY && defaultPosition[5].startX < defaultPosition[1].endX) {
          const diff = defaultPosition[5].endY - defaultPosition[1].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[1][backgroundList[1].length - i - 1].width = (defaultPosition[5].startX - defaultPosition[1].startX) * oneXWidth.value + 7;
            backgroundList[1][backgroundList[1].length - i - 1].position = 0;
          }
        }

        if(defaultPosition[6].endY < defaultPosition[5].endY && defaultPosition[6].endY > defaultPosition[5].startY && defaultPosition[6].endX > defaultPosition[5].startX) {
          const diff = defaultPosition[6].endY - defaultPosition[5].startY;

          for(let i = 0; i < diff; i++) {
            backgroundList[6][i].width = (defaultPosition[5].startX - defaultPosition[6].startX) * oneXWidth.value + 7;
            backgroundList[6][i].position = 0;
          }
        }

      } else if(index === 6) {

        if(defaultPosition[2].endX > defaultPosition[6].startX && defaultPosition[2].startY < defaultPosition[6].endY) {

          const diff = defaultPosition[6].endY - defaultPosition[2].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[2][backgroundList[2].length - i - 1].width = (defaultPosition[6].startX - defaultPosition[2].startX) * oneXWidth.value + 7;
            backgroundList[2][backgroundList[2].length - i - 1].position = 0;
          }

        }

        if(defaultPosition[5].startX < defaultPosition[6].endX && defaultPosition[5].startY < defaultPosition[6].endY) {

          const diff = defaultPosition[6].endY - defaultPosition[2].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[5][backgroundList[5].length - i - 1].width = (defaultPosition[5].endX - defaultPosition[6].endX) * oneXWidth.value + 7;
            backgroundList[5][backgroundList[5].length - i - 1].position = (defaultPosition[6].endX - defaultPosition[5].startX) * oneXWidth.value;
          }

        }

        if(defaultPosition[0].endX > defaultPosition[6].startX && defaultPosition[0].startY < defaultPosition[6].endY) {

          const diff = defaultPosition[6].endY - defaultPosition[0].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[0][backgroundList[0].length - i - 1].width = (defaultPosition[6].startX - defaultPosition[0].startX) * oneXWidth.value + 7;
            backgroundList[0][backgroundList[0].length - i - 1].position = 0;
          }

        }

        if(defaultPosition[4].startX < defaultPosition[6].endX && defaultPosition[4].startY < defaultPosition[6].endY) {

          const diff = defaultPosition[6].endY - defaultPosition[4].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[4][backgroundList[4].length - i - 1].width = (defaultPosition[4].endX - defaultPosition[6].endX) * oneXWidth.value + 7;
            backgroundList[4][backgroundList[4].length - i - 1].position = (defaultPosition[6].endX - defaultPosition[4].startX) * oneXWidth.value;
          }

        }

        if(defaultPosition[3].endY < defaultPosition[6].endY && defaultPosition[3].endX > defaultPosition[6].startX) {

          const diff = defaultPosition[3].endY - defaultPosition[3].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[4][i].width = (defaultPosition[6].endX - defaultPosition[3].startX) * oneXWidth.value + 7;
            backgroundList[4][i].position = 0;
          }

        }

        if(defaultPosition[7].endY < defaultPosition[6].endY && defaultPosition[7].startX < defaultPosition[6].endX) {

          const diff = defaultPosition[7].endY - defaultPosition[7].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[7][i].width = (defaultPosition[7].endX - defaultPosition[6].endX) * oneXWidth.value + 7;
            backgroundList[7][i].position = (defaultPosition[6].endX - defaultPosition[7].startX) * oneXWidth.value;
          }

        }

      } else if(index === 7) {

        if(defaultPosition[1].startY < defaultPosition[7].endY && defaultPosition[1].endX > defaultPosition[7].startX) {

          const diff = defaultPosition[7].endY - defaultPosition[1].startY;
          for(let i = 0; i < diff; i++) {
            backgroundList[1][backgroundList[1].length - i - 1].width = (defaultPosition[7].startX - defaultPosition[1].startX) * oneXWidth.value + 7;
            backgroundList[1][backgroundList[1].length - i - 1].position = 0;
          }

        }

        if(defaultPosition[6].endY < defaultPosition[7].endY && defaultPosition[6].endX > defaultPosition[7].startX) {

          const diff = defaultPosition[7].endY - defaultPosition[6].endY;
          for(let i = 0; i < diff; i++) {
            backgroundList[6][i].width = (defaultPosition[7].startX - defaultPosition[6].startX) * oneXWidth.value + 7;
            backgroundList[6][i].position = 0;
          }

        }

      }

    }

    const currentItem = ref(2);
    // Выбор длока для поднятия его по оси Z
    function changeCurrentItem(index) {
      if(settingsActive.value) {
        currentItem.value = index;
      } else {
        emit('toggleMainModal', { index: contentList[index].id, state: true });
      }
    }

    watch(currentItem, () => {
      rebuildForCurrentItem(currentItem.value);
    })

    // CURRENT ITEM

    // FOR AXIS
    // Максимальное значение на оси Y
    const numberOfY = reactive({
      startY: 0,
      endY: 5
    });
    // Вычисление чисел, которые нужно ставить на ось Y
    const numberOfYAxis = computed(() => {
      return numberOfY.endY / 5;
    })
    // Максимальное значение на оси X
    const numberOfX = reactive({
      startX: 0,
      endX: 160
    });
    // Вычисление чисел, которые нужно поставить на ось X
    const numberOfXAxis = computed(() => {
      return numberOfX.endX / 9;
    })

    // FOR AXIS

    // ONE DIVISION SIZE
    // Вычисление длины одного деления оси X в пикселях
    const oneXWidth = computed(() => {
      return graph.width / numberOfX.endX;
    });
    // Вычисление длины одного деления оси Y в пикселях
    const oneYHeight = computed(() => {
      return graph.height / numberOfY.endY;
    });

    // ONE DIVISION SIZE

    // MODAL
    // Получение данных из модалки
    function takeModalData(data) {
      numberOfX.endX = Number(data.endX);
      numberOfY.endY = Number(data.endY);
      // Обновление значений блоков в зависимости от осей
      updateDefault();
    }

    // Состояние модального окна с диапазонами
    const modalIsActive = ref(false);
    // Переключение состояния модального окна с диапазонами
    function toggleModal() {
      modalIsActive.value = !modalIsActive.value
    }

    // MODAL

    // UPDATE DEFAULT POSITION FOR BLOCKS
    // Перерасчет процентовки занимаемого блоком пространства

    function updateFactor() {
      factorList.forEach((item, index) => {
        item.factorStartX = defaultPosition[index].startX / numberOfX.endX;
        item.factorEndX = defaultPosition[index].endX / numberOfX.endX;
        item.factorStartY = defaultPosition[index].startY / numberOfY.endY;
        item.factorEndY = defaultPosition[index].endY / numberOfY.endY;
      })
    }
    updateFactor();

    // Изменение значений блоков
    function updateDefault() {
      defaultPosition.forEach((item, index) => {

        item.startX = numberOfX.endX * factorList[index].factorStartX;
        item.endX = numberOfX.endX * factorList[index].factorEndX;
        item.startY = numberOfY.endY * factorList[index].factorStartY;
        item.endY = numberOfY.endY * factorList[index].factorEndY;

      })
    }

    // UPDATE DEFAULT POSITION FOR BLOCKS

    // SIZES OF WRAPPER
    // Параметры обертки графика
    const graphWrapper = ref(null);
    const graph = reactive({
      width: 0,
      height: 0
    })
    // Расчет высоты графика пропорционально ширине
    function graphHeight() {
      return graph.width / 2.63
    }
    // Стили накладываемые на обертку графика
    const graphStyle = computed(() => {
      if(graphWrapper.value) {
        return 'height: ' + graphHeight() + 'px;'
      }
      return 0;
    })

    // SIZES OF WRAPPER

    onMounted(() => {
      graph.width = graphWrapper.value.clientWidth;
      graphHeight();

      setTimeout(() => {
        graph.height = graphWrapper.value.clientHeight;
      }, 10)

      window.addEventListener('resize', () => {
        graph.width = graphWrapper.value.clientWidth;
        graph.height = graphWrapper.value.clientHeight;
        graphHeight();
      })
    })

    return {
      settingsActive,
      settingToggle,
      graphWrapper,
      graphStyle,
      defaultPosition,
      oneXWidth,
      oneYHeight,
      changeCurrentItem,
      currentItem,
      modalIsActive,
      toggleModal,
      numberOfY,
      numberOfX,
      takeModalData,
      numberOfXAxis,
      numberOfYAxis,
      contentList,
      takeItemParams,
      takeArrowX,
      takeArrowY,
      isLimit,
      takeBackground,
      backgroundList
    }
  }
}
</script>

<style src="../assets/css/main.css"></style>