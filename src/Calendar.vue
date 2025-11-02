<script>
export default {
  name: "Calendar",
  props: {
    initialDate: {
      type: String,
      default: null,
    },
    defaultLocale: {
      type: String,
      default: "ru",
      validator: (value) => ["ru", "en"].includes(value),
    },
  },
  data() {
    return {
      currentDate: null,
      currentYear: null,
      currentMonth: null,
      locale: this.defaultLocale, // начальное значение из пропса
    };
  },
  computed: {
    daysOfWeek() {
      const days = ["пн", "вт", "ср", "чт", "пт", "сб", "вс"];
      return this.locale === "en" ? ["Su", "Mo", "Tu", "We", "Th", "Fr", "Sa"] : days;
    },
    monthNames() {
      const months = ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"];
      return this.locale === "en" ? ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"] : months;
    },
    currentMonthName() {
      return this.monthNames[this.currentMonth];
    },
    daysInMonth() {
      const date = new Date(this.currentYear, this.currentMonth + 1, 0);
      const totalDays = date.getDate();

      // Получаем день недели первого дня месяца (0 — воскресенье)
      const firstDayWeekday = new Date(this.currentYear, this.currentMonth, 1).getDay();
      const startOffset = this.locale === "en" ? firstDayWeekday : firstDayWeekday === 0 ? 6 : firstDayWeekday - 1;

      const days = [];
      // Заполняем пустые ячейки до первого дня месяца
      for (let i = 0; i < startOffset; i++) {
        days.push(null);
      }
      // Добавляем дни месяца
      for (let i = 1; i <= totalDays; i++) {
        days.push(i);
      }
      return days;
    },
  },
  methods: {
    prevMonth() {
      if (this.currentMonth === 0) {
        this.currentMonth = 11;
        this.currentYear--;
      } else {
        this.currentMonth--;
      }
    },
    nextMonth() {
      if (this.currentMonth === 11) {
        this.currentMonth = 0;
        this.currentYear++;
      } else {
        this.currentMonth++;
      }
    },
    selectDate(day) {
      if (!day) return; // пропускаем пустые ячейки

      const selectedDate = new Date(this.currentYear, this.currentMonth, day);
      this.currentDate = selectedDate;

      // Генерируем событие с выбранной датой в формате YYYY-MM-DD
      const formattedDate = selectedDate.toISOString().split("T")[0];
      this.$emit("date-selected", formattedDate);
    },
    isSelected(day) {
      if (!day || !this.currentDate) return false;
      const current = new Date(this.currentDate);
      return day === current.getDate() && this.currentMonth === current.getMonth() && this.currentYear === current.getFullYear();
    },
    initDate() {
      let date;
      if (this.initialDate) {
        date = new Date(this.initialDate);
      } else {
        date = new Date();
      }

      this.currentYear = date.getFullYear();
      this.currentMonth = date.getMonth();
      this.currentDate = date;
    },
    toggleLocale() {
      this.locale = this.locale === "ru" ? "en" : "ru";
      // Генерируем событие о смене языка
      this.$emit("locale-changed", this.locale);
    },
  },
  mounted() {
    this.initDate();
  },
};
</script>

<template>
  <div class="wrap-calendar">
    <p class="calendar-title">Календарь со сменой языка</p>
    <div class="wrap-locale-btn">
      <button class="locale-btn" @click="toggleLocale">
        {{ locale === "ru" ? "RU" : "EN" }}
      </button>
    </div>
    <div class="calendar">
      <div class="calendar-controls">
        <button class="nav-btn" @click="prevMonth">&lt;</button>
        <span class="month-year">{{ currentMonthName }} {{ currentYear }}</span>
        <button class="nav-btn" @click="nextMonth">&gt;</button>
      </div>

      <div class="week-days">
        <div class="day-name" v-for="day in daysOfWeek" :key="day">
          {{ day }}
        </div>
      </div>

      <div class="days-grid">
        <div v-for="(day, index) in daysInMonth" :key="index" :class="['day-cell', { selected: isSelected(day), disabled: !day }]" @click="selectDate(day)">
          {{ day || "" }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.wrap-calendar {
  display: flex;
  flex-direction: column;
  width: 400px;
  margin: 40px auto;
  gap: 20px;
}
.calendar-title {
  font-size: 20px;
  text-align: center;
  margin: 0;
}
.calendar {
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
}
.calendar-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background-color: #f0f0f0;
  border-bottom: 1px solid #ddd;
}
.nav-btn,
.locale-btn {
  background: none;
  border: none;
  font-size: 18px;
  cursor: pointer;
  color: #333;
}
.wrap-locale-btn {
  display: flex;
  justify-content: end;
}
.locale-btn {
  font-weight: bold;
  text-transform: uppercase;
  font-size: 16px;
  background-color: #f0f0f0;
  border-radius: 6px;
  padding: 5px 10px;
}
.month-year {
  font-weight: bold;
  font-size: 18px;
}
.week-days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
  padding: 5px 0;
  background-color: #eaeaea;
}
.day-name {
  padding: 5px;
  font-weight: 600;
  font-size: 0.8em;
  text-transform: uppercase;
}
.days-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 1px;
}
.day-cell {
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  background-color: #fff;
  transition: background-color 0.3s;
  border: 1px solid transparent;
}
.day-cell:hover {
  background-color: #f0f0f0;
  border-color: #ddd;
}
.day-cell.selected {
  background-color: #4caf50;
  color: white;
  font-weight: bold;
  border-color: #45a049;
}
.day-cell.disabled {
  background-color: #eee;
  color: #aaa;
  cursor: default;
}
</style>
