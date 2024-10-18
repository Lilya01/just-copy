<template>
  <div class="calendar">
    <input
      type="text"
      v-model="formattedDate"
      @focus="() => openCalendar()"
      readonly
    />
    <div v-if="showCalendar" class="calendar-dropdown">
    <div class="calendar-header">
      <button class="calendar-button" @click="() => changeYear(-1)">«</button>
      <button class="calendar-button" @click="() => changeMonth(-1)">‹</button>
      <span>{{ monthNames[currentMonth] }} {{ currentYear }}</span>
      <button class="calendar-button" @click="() => changeMonth(1)">›</button>
      <button class="calendar-button" @click="() => changeYear(1)">»</button>
    </div>
      <div class="calendar-grid">
        <div class="day-name" v-for="day in dayNames" :key="day">{{ day }}</div>
        <div
          class="day"
          v-for="(date, index) in fullMonthDays"
          :key="index"
          :class="{'day--other-month': date.isOtherMonth}"
          @click="() => handleDateClick(date)"
        >
          {{ date.day }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentYear: new Date().getFullYear(),
      currentMonth: new Date().getMonth(),
      showCalendar: false,
      formattedDate: ''
    };
  },
  computed: {
    dayNames() {
      return ['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб'];
    },
    monthNames() {
      return [
        'Январь', 'Февраль', 'Март', 'Апрель', 'Май', 
        'Июнь', 'Июль', 'Август', 'Сентябрь', 
        'Октябрь', 'Ноябрь', 'Декабрь'
      ];
    },
    daysInMonth() {
      const date = new Date(this.currentYear, this.currentMonth + 1, 0);
      return Array.from({ length: date.getDate() }, (_, i) => ({ day: i + 1, isOtherMonth: false }));
    },
    blanks() {
      const firstDayOfMonth = new Date(this.currentYear, this.currentMonth, 1).getDay();
      return Array.from({ length: firstDayOfMonth });
    },
    fullMonthDays() {
      const previousMonthDays = this.getPreviousMonthDays();
      const currentMonthDays = this.daysInMonth;
      const nextMonthDays = this.getNextMonthDays();
      return [...previousMonthDays, ...currentMonthDays, ...nextMonthDays];
    }
  },
  methods: {
    changeMonth(amount) {
      this.currentMonth += amount;
      if (this.currentMonth < 0) {
        this.currentMonth = 11;
        this.currentYear--;
      } else if (this.currentMonth > 11) {
        this.currentMonth = 0;
        this.currentYear++;
      }
    },
    changeYear(amount) {
      this.currentYear += amount;
    },
    openCalendar() {
      this.showCalendar = true
    },
    formatDate(date) {
      return `${this.currentYear}-${String(this.currentMonth + 1).padStart(2, '0')}-${String(date).padStart(2, '0')}`
    },
    selectDate(date) {
      this.formattedDate = this.formatDate(date);
      this.showCalendar = false;
    },
    getPreviousMonthDays() {
      const firstDayOfMonth = new Date(this.currentYear, this.currentMonth, 1).getDay();
      const prevMonth = new Date(this.currentYear, this.currentMonth, 0);
      const daysInPrevMonth = prevMonth.getDate();
      return Array.from({ length: firstDayOfMonth }, (_, i) => ({
        day: daysInPrevMonth - firstDayOfMonth + 1 + i,
        isOtherMonth: true
      }));
    },
    getNextMonthDays() {
      const totalDaysInMonth = this.daysInMonth.length;
      const lastDayOfMonth = new Date(this.currentYear, this.currentMonth + 1, 0).getDay();
      return Array.from({ length: 6 - lastDayOfMonth }, (_, i) => ({
        day: i + 1,
        isOtherMonth: true
      }));
    },
    handleDateClick(date) {
      if (!date.isOtherMonth) {
        this.selectDate(date.day);
      }
    }
  }
};
</script>

<style scoped lang="less">
  @import "styles.less";
</style>