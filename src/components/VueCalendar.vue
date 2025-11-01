<template>
  <div class="vue-calendar">
    <div class="calendar-header">
      <button @click="previousMonth" class="nav-button">&lt;</button>
      <div class="month-year">
        {{ getMonthName(currentMonth) }} {{ currentYear }}
      </div>
      <button @click="nextMonth" class="nav-button">&gt;</button>
    </div>

    <div class="calendar-weekdays">
      <div
        v-for="day in weekdays"
        :key="day"
        class="weekday"
      >
        {{ day }}
      </div>
    </div>

    <div class="calendar-days">
      <div
        v-for="(day, index) in calendarDays"
        :key="index"
        :class="[
          'calendar-day',
          {
            'other-month': day.isOtherMonth,
            'today': day.isToday,
            'selected': day.isSelected
          }
        ]"
        @click="selectDate(day.date)"
      >
        {{ day.day }}
      </div>
    </div>
  </div>
</template>

<script>
import '../assets/styles/calendar.css'

export default {
  name: 'VueCalendar',
  props: {
    initialDate: {
      type: String,
      default: null
    },
    locale: {
      type: String,
      default: 'ru'
    }
  },
  data() {
    const now = new Date()
    now.setHours(0, 0, 0, 0)
    
    return {
      currentDate: now,
      selectedDate: now,
      locales: {
        ru: {
          months: [
            'Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь',
            'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'
          ],
          weekdays: ['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб']
        },
        en: {
          months: [
            'January', 'February', 'March', 'April', 'May', 'June',
            'July', 'August', 'September', 'October', 'November', 'December'
          ],
          monthsShort: [
            'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
            'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'
          ],
          weekdays: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
        },
        de: {
          months: [
            'Januar', 'Februar', 'März', 'April', 'Mai', 'Juni',
            'Juli', 'August', 'September', 'Oktober', 'November', 'Dezember'
          ],
          weekdays: ['So', 'Mo', 'Di', 'Mi', 'Do', 'Fr', 'Sa']
        },
        fr: {
          months: [
            'Janvier', 'Février', 'Mars', 'Avril', 'Mai', 'Juin',
            'Juillet', 'Août', 'Septembre', 'Octobre', 'Novembre', 'Décembre'
          ],
          weekdays: ['Dim', 'Lun', 'Mar', 'Mer', 'Jeu', 'Ven', 'Sam']
        }
      }
    }
  },
  computed: {
    currentYear() {
      if (!this.currentDate) return new Date().getFullYear()
      return this.currentDate.getFullYear()
    },
    currentMonth() {
      if (!this.currentDate) return new Date().getMonth()
      return this.currentDate.getMonth()
    },
    weekdays() {
      return this.locales[this.locale]?.weekdays || this.locales.ru.weekdays
    },
    calendarDays() {
      if (!this.currentDate) return []
      
      const year = this.currentYear
      const month = this.currentMonth
      
      const firstDay = new Date(year, month, 1)
      const lastDay = new Date(year, month + 1, 0)
      
      let firstDayOfWeek = firstDay.getDay()
      
      const daysInMonth = lastDay.getDate()
      
      const today = new Date()
      today.setHours(0, 0, 0, 0)
      
      const selected = this.selectedDate ? new Date(this.selectedDate) : null
      if (selected) {
        selected.setHours(0, 0, 0, 0)
      }
      
      const days = []
      
      const prevMonth = new Date(year, month - 1, 0)
      const daysInPrevMonth = prevMonth.getDate()
      
      for (let i = firstDayOfWeek - 1; i >= 0; i--) {
        const day = daysInPrevMonth - i
        const date = new Date(year, month - 1, day)
        days.push({
          day: day,
          date: date,
          isOtherMonth: true,
          isToday: this.isSameDay(date, today),
          isSelected: selected ? this.isSameDay(date, selected) : false
        })
      }
      
      for (let day = 1; day <= daysInMonth; day++) {
        const date = new Date(year, month, day)
        days.push({
          day: day,
          date: date,
          isOtherMonth: false,
          isToday: this.isSameDay(date, today),
          isSelected: selected ? this.isSameDay(date, selected) : false
        })
      }
      
      const remainingDays = 42 - days.length
      for (let day = 1; day <= remainingDays; day++) {
        const date = new Date(year, month + 1, day)
        days.push({
          day: day,
          date: date,
          isOtherMonth: true,
          isToday: this.isSameDay(date, today),
          isSelected: selected ? this.isSameDay(date, selected) : false
        })
      }
      
      return days
    }
  },
  created() {
    this.initializeDate()
  },
  watch: {
    initialDate() {
      this.initializeDate()
    },
    locale() {
    }
  },
  methods: {
    initializeDate() {
      let newSelectedDate
      
      // Если initialDate не задан или пустой, используем текущую дату
      if (!this.initialDate || (typeof this.initialDate === 'string' && this.initialDate.trim() === '')) {
        const today = new Date()
        today.setHours(0, 0, 0, 0)
        this.currentDate = new Date(today)
        this.selectedDate = new Date(today)
        newSelectedDate = today
      } else {
        // Если initialDate задан, парсим его
        const dateParts = this.initialDate.split('-')
        if (dateParts.length === 3) {
          const year = parseInt(dateParts[0], 10)
          const month = parseInt(dateParts[1], 10) - 1
          const day = parseInt(dateParts[2], 10)
          
          const parsedDate = new Date(year, month, day)
          parsedDate.setHours(0, 0, 0, 0)
          
          // Проверяем валидность даты
          if (
            parsedDate.getFullYear() === year &&
            parsedDate.getMonth() === month &&
            parsedDate.getDate() === day
          ) {
            this.currentDate = parsedDate
            this.selectedDate = new Date(parsedDate)
            newSelectedDate = parsedDate
          } else {
            // Если дата невалидна, используем текущую дату
            const today = new Date()
            today.setHours(0, 0, 0, 0)
            this.currentDate = new Date(today)
            this.selectedDate = new Date(today)
            newSelectedDate = today
          }
        } else {
          // Если формат неверный, используем текущую дату
          const today = new Date()
          today.setHours(0, 0, 0, 0)
          this.currentDate = new Date(today)
          this.selectedDate = new Date(today)
          newSelectedDate = today
        }
      }
      
      // Убеждаемся, что часы сброшены
      this.currentDate.setHours(0, 0, 0, 0)
      if (this.selectedDate) {
        this.selectedDate.setHours(0, 0, 0, 0)
      }
      
      // Эмитим событие с выбранной датой
      if (newSelectedDate) {
        const formattedDate = this.formatDate(newSelectedDate)
        this.$emit('date-selected', formattedDate)
        this.$emit('dateSelected', formattedDate)
      }
    },
    previousMonth() {
      this.currentDate = new Date(this.currentYear, this.currentMonth - 1, 1)
    },
    nextMonth() {
      this.currentDate = new Date(this.currentYear, this.currentMonth + 1, 1)
    },
    selectDate(date) {
      this.selectedDate = new Date(date)
      this.selectedDate.setHours(0, 0, 0, 0)
      
      if (date.getMonth() !== this.currentMonth || date.getFullYear() !== this.currentYear) {
        this.currentDate = new Date(date)
      }
      
      const formattedDate = this.formatDate(date)
      
      this.$emit('date-selected', formattedDate)
      this.$emit('dateSelected', formattedDate)
    },
    formatDate(date) {
      const year = date.getFullYear()
      const month = String(date.getMonth() + 1).padStart(2, '0')
      const day = String(date.getDate()).padStart(2, '0')
      return `${year}-${month}-${day}`
    },
    isSameDay(date1, date2) {
      return date1.getFullYear() === date2.getFullYear() &&
             date1.getMonth() === date2.getMonth() &&
             date1.getDate() === date2.getDate()
    },
    getMonthName(monthIndex) {
      const localeData = this.locales[this.locale] || this.locales.ru
      if (this.locale === 'en' && localeData.monthsShort) {
        return localeData.monthsShort[monthIndex]
      }
      return localeData.months[monthIndex]
    }
  }
}
</script>

