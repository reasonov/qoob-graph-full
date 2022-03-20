<template>
  <div class="preview-overlay">
    <div class="preview__wrapper">
      <p class="preview__text">
        Здавствуйте Александр!
        Напоминаем Вам, что 12 ноября 17:00,
        вы записаны к Светлане, на депиляцию.
        Ваша команда “Health & Beauty”.
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: "preview-overlay"
}
</script>

<style scoped>
  .preview-overlay {
    position: absolute;
    bottom: 0;
    left: 0;

    width: 100%;
    height: 218px;
    padding: 25px 20px;

    box-sizing: border-box;
    border-radius: 12px 12px 0 0;

    background-color: #F6F9FC;
  }

  .preview__wrapper {
    width: 341px;
    height: 128px;
    padding: 15px;
    margin-top: 15px;
    margin-left: 40px;

    border-radius: 18px 18px 18px 2px;
    box-sizing: border-box;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.06), 0 1px 1px rgba(0, 0, 0, 0.08);

    background: #FFFFFF;
  }

  .preview__text {
    font-size: 16px;
    line-height: 24px;
  }
</style>