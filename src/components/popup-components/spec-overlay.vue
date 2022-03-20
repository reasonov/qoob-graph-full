<template>
  <div class="spec-overlay">
    <ul class="overlay__list">

      <li :class="['overlay__item', 'overlay__item' + index]" v-for="(item, index) in overlayList" :key="item">
        <button
            :class="['text--color', 'text--' + item.color]"
            @click="addVariable(index)"
        >
          {{ item.text }}
        </button>
      </li>

    </ul>
  </div>
</template>

<script>

export default {
  name: "spec-overlay",
  emits: ['addVariable'],
  setup(props, { emit }) {

    const overlayList = [
      {
        text: 'дата и время',
        color: 'blue'
      },
      {
        text: 'часы и минуты',
        color: 'blue'
      },
      {
        text: 'дата событитя',
        color: 'blue'
      },
      {
        text: 'дней до события',
        color: 'blue'
      },
      {
        text: 'имя клиента',
        color: 'red'
      },
      {
        text: 'имя сотрудника',
        color: 'red'
      },
      {
        text: 'название должности',
        color: 'green'
      },
      {
        text: 'название услуги',
        color: 'green'
      },{
        text: 'ссылка на карту лояльности',
        color: 'dark-blue'
      },
      {
        text: 'скидка в процентах',
        color: 'dark-blue'
      },
      {
        text: 'ссылка на виджет ycliens',
        color: 'purple'
      },
      {
        text: 'номер телефона',
        color: 'purple'
      }
    ]

    function addVariable(index) {
      emit('addVariable', index);
    }

    return { overlayList, addVariable }

  }
}
</script>

<style scoped>

.spec-overlay {
  position: absolute;
  bottom: 0;
  left: 0;

  width: 100%;
  height: 217px;
  padding: 35px 40px;

  box-sizing: border-box;
  border-radius: 12px 12px 0 0;

  background: #F6F9FC;
}

.overlay__list {
  display: grid;
  grid-template-columns: 150px 175px 235px;
  grid-template-rows: 15px 15px 15px 15px;
  grid-column-gap: 20px;
  grid-row-gap: 25px;
}

.overlay__item0 {
  grid-column: 1;
  grid-row: 1;
}

.overlay__item1 {
  grid-column: 1;
  grid-row: 2;
}

.overlay__item2 {
  grid-column: 1;
  grid-row: 3;
}

.overlay__item3 {
  grid-column: 1;
  grid-row: 4;
}

.overlay__item4 {
  grid-column: 2;
  grid-row: 1;
}

.overlay__item5 {
  grid-column: 2;
  grid-row: 2;
}

.overlay__item6 {
  grid-column: 2;
  grid-row: 3;
}

.overlay__item7 {
  grid-column: 2;
  grid-row: 4;
}

.text--color {
  padding: 2px 10px 4px 10px;
  border-radius: 49px;

  font-size: 14px;
  color: #fff;
}

.text--color:hover {
  transform: scale(1.05);
  box-shadow: 0px 0px 6px 2px rgba(0, 0, 0, 0.2);
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
</style>