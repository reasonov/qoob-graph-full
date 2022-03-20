<template>
  <div class="popup-segment__header">
    <span class="item-number segment__number">0{{ aSideNumber - 1 }}</span>
    <h2 class="header__title">{{ nameList[aSideNumber - 2] }}</h2>
  </div>
</template>

<script>
export default {
  name: "segmeny-header",
  props: {
    aSideNumber: {
      type: Number,
      required: true
    }
  },
  setup() {
    const nameList = [
        'Чемпионы',
        'Лояльные',
        'Растущие',
        'Новички',
        'В зоне риска',
        'Средние на грани',
        'Сомневающиеся',
        'Требуют внимания',
        'Спящие'
    ]

    return { nameList }

  }
}
</script>

<style scoped>

</style>