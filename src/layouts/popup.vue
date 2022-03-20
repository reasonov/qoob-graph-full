<template>

  <div class="popup">

    <div class="popup__background"></div>


    <notification-editor
        :currentItem="itemList[editorIndex]"
        @toggleModal="takeModalState($event)"
        :notifEditorState="notifEditorState"
    />


    <modal-new-notification
        @addNotification="takeModalState($event)"
        @closePopup="takeModalState({modal: 'newNotification', state: false})"
        :newModalState="newModalState"
    />

    <modal-submit-settings
        @toggleModal="takeModalState($event)"
        :submitModalState="submitModalState"
    />

    <div class="main-wrapper">

      <div class="main__a-side">
        <ul class="a-side__list">
          <li
              :class="['a-side__item', 'a-side__item' + index, {'a-side--active': aSideNumber === index}]"
              @click="updateASide(index)"
              v-for="index in 10"
              :key="index"
          >
            <button class="a-side__button">
              <redo-icon v-if="index === 1" />

              <span class="item-number a-side__number" v-else>0{{ index - 1 }}</span>

            </button>
          </li>
        </ul>
      </div>

      <section class="popup-segment">

        <segment-header :aSideNumber="aSideNumber" />

        <div class="stat">

          <h3 class="segment__title title">Статистика по сегменту</h3>

          <ul class="stat__list">
            <li class="stat__item">
              <p class="stat__text">Клиентов</p>
              <p class="stat__text text--bold">4 180</p>
            </li>
            <li class="stat__item">
              <p class="stat__text">От общего числа</p>
              <p class="stat__text text--bold">38%</p>
            </li>
          </ul>

          <div class="graph">
            <div class="popup-graph__item popup-graph__item1">
              <div class="graph__header">
                <h4 class="graph__title">Переходов за период</h4>
                <trend-up-icon />
              </div>

              <ul class="graph__list">
                <li class="graph__section">
                  <p class="graph__text">Средние, на грани</p>
                  <p class="graph__text graph__text--bold">+ 6</p>
                </li>
                <li class="graph__section">
                  <p class="graph__text">Новички</p>
                  <p class="graph__text graph__text--bold">+ 2</p>
                </li>
              </ul>

            </div>

            <div class="popup-graph__item popup-graph__item2">
              <div class="graph__header">
                <h4 class="graph__title">Ближайшие потери</h4>
                <trend-down-icon />
              </div>

              <ul class="graph__list">
                <li class="graph__section">
                  <p class="graph__text">В зоне риска</p>
                  <div class="graph-item__bold">
                    <p class="graph__time">5 дн</p>
                    <p class="graph__text text--bold">&nbsp;‒ 5</p>
                  </div>
                </li>

                <li class="graph__section">
                  <p class="graph__text">Новички</p>
                  <div class="graph-item__bold">
                    <p class="graph__time">16 ч</p>
                    <p class="graph__text text--bold">&nbsp;‒ 4</p>
                  </div>
                </li>
              </ul>

            </div>
          </div>

        </div>

        <div class="params">
          <h3 class="params__title title">Параметры сегмента</h3>

          <ul class="params__list">
            <li class="params__item">
              <p class="params__text">Давность, дней</p>
              <p class="params__text params__text--bold">0 ‒ 149</p>
            </li>
            <li class="params__item">
              <p class="params__text">Количество покупок</p>
              <p class="params__text params__text--bold">2 ‒ 4</p>
            </li>
            <li class="params__item">
              <p class="params__text">Общая сумма покупок</p>
              <p class="params__text params__text--bold">600 ₽ ‒ 8 000 ₽</p>
            </li>
          </ul>
        </div>

        <div class="guidance">
          <h3 class="guidance__title title">Рекомендации по сегменту</h3>

          <p class="guidance__text guidance__text1">
            Являясь всего лишь частью общей картины, предприниматели в сети интернет
            лишь добавляют фракционных разногласий и указаны как претенденты на роль ключевых факторов.
            В своём стремлении повысить качество жизни, они забывают, что постоянный количественный
            рост и сфера нашей активности требует анализа дальнейших направлений развития.
            Как уже неоднократно упомянуто, действия представителей оппозиции,
            превозмогая сложившуюся непростую экономическую ситуацию, обнародованы.
          </p>

          <p class="guidance__text">
            В своём стремлении повысить качество жизни, они забывают, что постоянный количественный
            рост и сфера нашей активности требует анализа дальнейших направлений развития.
            Как уже неоднократно упомянуто, действия представителей оппозиции,
            превозмогая сложившуюся непростую экономическую ситуацию, обнародованы.
          </p>
        </div>

      </section>

      <section class="notif">

        <div class="notif__header">
          <h3 class="notif__title title">Типы уведомлений сегмента</h3>

          <button class="notif__add" @click="takeModalState({modal: 'newNotification', state: true})">
            Добавить тип уведомлений
            <add-icon class="add-icon" />
          </button>
        </div>

        <div class="notif__params">
          <div class="params__left">
            <p class="params__text params__text1">#</p>
            <p class="params__text params__text2">Название типа уведомлений</p>
          </div>
          <div class="params__right">
            <p class="params__text params__text3">Статус</p>
            <p class="params__text params__text4">Действие</p>
          </div>
        </div>

        <ul class="notif__list">
          <transition-group name="swipeAm">
            <li class="notif-item" v-for="item in activeList" :key="item">
              <notification-item
                  :itemIndex="item.index"
                  :itemText="item.text"
                  @takeNewModal="takeModalState($event)"
                  @deleteItem="deleteItem($event)"
              />
            </li>
          </transition-group>
        </ul>

        <div class="submit-block">
          <div class="submit__left">
            <h3 class="submit__title title">Рассылка уведомлений</h3>
            <p class="submit__number">
              Всего клиентов:
              <sms-small-icon class="sms--small"/>
              <span class="text--bold">4&nbsp;180</span>
            </p>
          </div>

          <button class="submit__export submit-button" @click="takeModalState({modal: 'submitSettings', state: true})">Создать рассылку</button>

        </div>

      </section>
    </div>
  </div>

</template>

<script>
import { ref, reactive, watchEffect } from "vue";
import segmentHeader from "@/components/popup-components/segment-header";
import notificationItem from "@/components/popup-components/notification-item";
import trendUpIcon from "@/components/popup-components/icon/trend-up-icon";
import trendDownIcon from "@/components/popup-components/icon/trend-down-icon";
import smsSmallIcon from "@/components/popup-components/icon/sms-small-icon";
import addIcon from "@/components/popup-components/icon/add-icon";
import notificationEditor from "@/components/popup-components/notification-editor";
import modalNewNotification from "@/components/popup-components/modal-new-notification";
import modalSubmitSettings from "@/components/popup-components/modal-submit-settings";
import redoIcon from "@/components/popup-components/icon/redo-icon";

export default {
  name: 'App',
  components: {
    segmentHeader: segmentHeader,
    notificationItem: notificationItem,
    trendUpIcon: trendUpIcon,
    trendDownIcon: trendDownIcon,
    addIcon: addIcon,
    smsSmallIcon: smsSmallIcon,
    notificationEditor: notificationEditor,
    modalNewNotification: modalNewNotification,
    modalSubmitSettings: modalSubmitSettings,
    redoIcon: redoIcon
  },
  props: {
    segmentPopup: {
      type: Number,
      default: 2
    }
  },
  setup(props, { emit }) {
    const itemList = reactive([]);

    const newModalState = ref(false);

    const notifEditorState = ref(false);

    const submitModalState = ref(false);

    const editorIndex = ref(0);

    function takeModalState(data) {

      if(data.modal === 'newNotification') {
        newModalState.value = data.state;

        if(data.list) {
          data.list.forEach((item, index, arr) => {
            if(itemList.length >= arr.length) {

              if(item.state != itemList[index].state) {
                itemList[index].state = item.state;
              }

            } else {
              itemList.push(data.list[index]);
            }
          })
          updateActiveList();
        }

      } else if(data.modal === 'submitSettings') {
        submitModalState.value = data.state;
      }else {

        if(data.index) {
          editorIndex.value = data.index;
        }

        if(data.type === 'submit') {
          itemList[editorIndex.value].text = data.updateName;
        }

        notifEditorState.value = data.state;
      }
    }

    const activeList = reactive([]);

    function updateActiveList() {
      for(let i = 0; i <= itemList.length; i++) {
        activeList.pop();
      }
      itemList.forEach((item) => {
        if(item.state) {
          activeList.push(item);
        }
      })
    }

    function deleteItem(index) {
      itemList[index].state = false;
      updateActiveList();
    }

    const aSideNumber = ref(2);

    function updateASide(index) {
      if(index === 1) {
        emit('toggleMainModal', false);
      } else {
        aSideNumber.value = index;
      }
    }

    watchEffect(() => {
      aSideNumber.value = props.segmentPopup;
    })

    return {
      itemList,
      notifEditorState,
      newModalState,
      takeModalState,
      deleteItem,
      submitModalState,
      editorIndex,
      activeList,
      aSideNumber,
      updateASide
    }
  }
}
</script>

<style src="../assets/fonts/style.css"></style>
<style src="../assets/css/popup.css"></style>

<style scoped>

.swipeAm-enter-active,
.swipeAm-leave-active {
  transition: all 0.3s ease;
}

.swipeAm-enter-from {
  transform: translateX(-50px);
  opacity: 0;
}

.swipeAm-leave-to {
  transform: translateX(50px);
  opacity: 0;
}
</style>
