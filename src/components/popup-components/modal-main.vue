<template>
  <div class="modal-main">
    <div class="section-wrapper">
      <button
          :class="['main__section', 'main__section1', {'main__section--active': activeSection === 0}]"
          @click="selectSection(0)"
      >
        Push
      </button>
      <button
          :class="['main__section', 'main__section2', {'main__section--active': activeSection === 1}]"
          @click="selectSection(1)"
      >
        WhatsApp
      </button>
      <button
          :class="['main__section', 'main__section3', {'main__section--active': activeSection === 2}]"
          @click="selectSection(2)"
      >
        SMS
      </button>
    </div>

    <div class="main__middle">


      <VueEmoji
          ref="emoji1"
          @input="onInput"
          :value="myText[0]"
          v-show="activeSection === 0"
      />
      <VueEmoji
          ref="emoji2"
          @input="onInput"
          :value="myText[1]"
          v-show="activeSection === 1"
      />
      <VueEmoji
          ref="emoji3"
          @input="onInput"
          :value="myText[2]"
          v-show="activeSection === 2"
      />


      <transition name="overlayAm">
        <spec-overlay v-show="overlayState === 1" @addVariable="addVariable($event)" />
      </transition>

    </div>

    <div class="main__bottom">
      <button class="bottom__button bottom__button1" @click="toggleOverlay(1)">Переменные</button>

      <button class="bottom__button bottom__button4" @click="clearTextarea">Очистить</button>
    </div>
  </div>

</template>

<script>
import {reactive, ref} from "vue";
import specOverlay from "@/components/popup-components/spec-overlay";
import VueEmoji from 'emoji-vue'

export default {
  name: "modal-main",
  components: {
    specOverlay: specOverlay,
    VueEmoji: VueEmoji
  },
  setup() {
    const activeSection = ref(0);

    function selectSection(number) {
      activeSection.value = number;
    }

    const overlayState = ref(0);

    function toggleOverlay(index) {
      if(overlayState.value === index) {
        overlayState.value = 0;
      } else {
        overlayState.value = index;
      }
    }

    // 1-----------------------------------------------------------------------------------------------------

    const variableList = [
      {
        index: 1,
        code : '%DATETIME%',
        width: 115
      },
      {
        index: 2,
        code : '%HOURMINUTES%',
        width: 128
      },
      {
        index: 3,
        code : '%DATE%',
        width: 126
      },
      {
        index: 4,
        code : '%DAYS%',
        width: 142
      },
      {
        index: 5,
        code : '%CLIENT_NAME%',
        width: 111
      },
      {
        index: 6,
        code : '%MASTER_NAME%',
        width: 135
      },
      {
        index: 7,
        code : '%POSITION_TITLE%',
        width: 168
      },
      {
        index:8 ,
        code : '%SERVICE_TITLE%',
        width: 139
      },{
        index: 9,
        code : '%CARD_URL%',
        width: 219
      },
      {
        index: 10,
        code : '%DISCOUNT%%',
        width: 161
      },
      {
        index: 11,
        code : '%LINK%',
        width: 198
      },
      {
        index: 12,
        code : '%CONTACT_PHONE%',
        width: 140
      }
    ]

    function onInput(event) {
      console.log(event)
    }

    const emoji1 = ref(null);
    const emoji2 = ref(null);
    const emoji3 = ref(null);
    const myText = reactive(['', '', '']);

    function style(index) {
      return 'width: ' + variableList[index].width + 'px;';
    }

    function defaultText() {

      function imageURL(index) {
        return require('../../assets/img/variables/var' + index + '.png');
      }

      myText.forEach((item, index) => {
        myText[index] = `Здравствуйте <img style="${style(4)}" src="${imageURL(4)}" />!<br>
       Напоминаем вам, что <img style="${style(0)}" src="${imageURL(0)}" />,<br>
       вы записаны к <img style="${style(5)}" src="${imageURL(5)}" />, на <img style="${style(7)}" src="${imageURL(7)}" />.<br>
       Ваша команда "Health & Beauty".`
      })

    }
    defaultText();

    function clearTextarea(){
      if(activeSection.value === 0) {
        emoji1.value.clear();
      } else if(activeSection.value === 1) {
        emoji2.value.clear();
      } else if(activeSection.value === 2) {
        emoji3.value.clear();
      }
    }

    function addVariable(index) {
      let imageURL = require('../../assets/img/variables/var' + index + '.png');
      myText[activeSection.value] = `<img style="${style(index)}" src="${imageURL}" /> &nbsp;`;
      overlayState.value = 0;
    }

    // 2-----------------------------------------------------------------------------------------------------


    return {
      activeSection,
      selectSection,
      overlayState,
      toggleOverlay,
      variableList,
      emoji1,
      emoji2,
      emoji3,
      clearTextarea,
      myText,
      addVariable,
      onInput
    }
  }
}
</script>

<style scoped>

.close-icon {
  width: 8px;
  height: 8px;
  margin-left: 10px;

  background-image: url("../../assets/img/close-square.png");
}

.modal-main {
  position: relative;
  margin-top: 70px;
}

.main__middle {
  position: relative;

  height: 220px;
  padding: 30px;
  box-sizing: border-box;

  border-radius: 0 12px 0 0;

  background: #F6F9FC;
}

.section-wrapper {
  display: flex;
  height: 49px;
}

.main__section {
  position: relative;
  top: 7px;

  height: 42px;
  margin-right: 5px;

  padding: 12px 24px;
  border-radius: 12px 12px 0 0;
  box-sizing: border-box;
  transition: .2s all ease;

  font-size: 14px;

  background: #F0F3F8;
}

.main__section--active {
  top: 0;
  height: 49px;
  background-color: #F6F9FC;
}

.main__bottom {
  display: flex;
  justify-content: space-between;
  align-items: center;

  height: 75px;
  border-top: 1px solid #E1E2E2;
  border-radius: 0 0 12px 12px;

  background: #F0F3F8;
}

.bottom__button {
  height: 44px;
  padding: 10px 24px;

  box-shadow: 0 1px 2px rgba(21, 21, 21, 0.15);
  border-radius: 12px;
  box-sizing: border-box;

  font-size: 14px;
  color: #6D6D6D;

  background: #FFFFFF;
}

.bottom__button1 {
  margin-left: 15px;
}

.bottom__button2 {
  margin-right: 15px;
}

.bottom__button4 {
  height: 44px;
  margin-right: 15px;
  box-sizing: border-box;
}

.text--color {
  padding: 5px 14px;
  border-radius: 49px;

  font-size: 14px;
  color: #fff;
}

.text--blue {
  background: #5CC3C6;
}

.text--red {
  background-color: #DD6068;
}

.text--green {
  background-color: #8ECD88;
}

.text--dark-blue {
  background-color: #328FE4;
}

.text--purple {
  background-color: #9D58A3;
}

.overlayAm-enter-active,
.overlayAm-leave-active {
  transition: 0.3s all ease;
}

.overlayAm-enter-from,
.overlayAm-leave-to {
  bottom: -30px;
  opacity: 0;
}

@media (max-width: 1440px) {

  .modal-main {
    margin-top: 80px;
  }

}

</style>