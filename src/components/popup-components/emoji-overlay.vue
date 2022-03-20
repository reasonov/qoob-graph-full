<template>
  <div class="emoji-overlay">
    <ul class="overlay__list">
      <li class="overlay__item" @click="addEmoji(index)" v-for="index in 28" :key="index">
        <img :src="require('../../assets/img/smile' + index + '.png')" alt="emoji-image">
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "emoji-overlay",
  emits: ['addEmoji'],
  setup(props, { emit }) {

    function addEmoji(index) {
      emit('addEmoji', index);
    }

    return { addEmoji }

  }
}
</script>

<style scoped>

.emoji-overlay {
  position: absolute;
  bottom: 0;
  left: 0;

  width: 100%;
  padding: 10px 20px;

  box-sizing: border-box;
  border-radius: 12px 12px 0 0;

  background-color: #F6F9FC;
}

.overlay__list {
  display: flex;
  flex-wrap: wrap;

  height: 193px;
}

.overlay__item {
  margin: 15px 20px;
}
</style>