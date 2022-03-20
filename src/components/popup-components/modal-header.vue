<template>
  <div class="body__header">

    <div class="input-wrapper">
      <label class="set-name__label header__text" for="set-name">Название типа уведомлений</label>
      <input id="set-name" type="text" placeholder="Напоминание клиенту о предстоящем визите">
    </div>

    <div class="mark-wrapper">
      <p class="mark__text header__text">Метка\Номер</p>

      <div class="set-mark-wrapper">
        <star-icon class="modal__star" />

        <span class="mark__line"></span>

        <button class="mark__button">
          <arrow-down-icon />
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import starIcon from "@/components/popup-components/icon/star-icon";
import arrowDownIcon from "@/components/popup-components/icon/arrow-down-icon";

export default {
  name: "modal-header",
  components: {
    starIcon: starIcon,
    arrowDownIcon: arrowDownIcon
  }
}
</script>

<style scoped>

</style>