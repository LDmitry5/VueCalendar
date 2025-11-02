<script setup>
import Calendar from "./Calendar.vue";

function onDateSelected(date) {
  console.log("Выбранная дата:", date);
}

function onLocaleChanged(newLocale) {
  console.log("Язык изменен на:", newLocale);
}
</script>

<template>
  <Calendar @date-selected="onDateSelected" @locale-changed="onLocaleChanged" />
</template>

<style scoped></style>
