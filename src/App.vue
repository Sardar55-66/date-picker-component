<template>
  <div id="app">
    <h1>Vue Calendar Component</h1>
    
    <div class="demo-section">
      <h2>Календарь с настройками</h2>
      
      <div class="controls">
        <div class="control-group">
          <label>Выберите язык: </label>
          <select v-model="currentLocale">
            <option value="ru">Русский</option>
            <option value="en">English</option>
            <option value="de">Deutsch</option>
            <option value="fr">Français</option>
          </select>
        </div>

        <div class="control-group date-input-wrapper">
          <label>Начальная дата (год-месяц-день): </label>
          <div class="date-input-container">
            <div class="input-row">
              <input 
                type="text" 
                v-model="initialDateInput" 
                placeholder="2024-12-25"
                :class="{ 'error': dateError }"
                @change="updateInitialDate"
                @input="clearDateError"
              />
              <button 
                v-if="initialDateInput.trim() !== ''"
                @click="updateInitialDate" 
                class="add-btn"
              >
                +
              </button>
              <button @click="clearInitialDate" class="clear-btn">Сбросить</button>
            </div>
            <div v-if="dateError" class="error-message">{{ dateError }}</div>
          </div>
        </div>
      </div>

      <VueCalendar 
        :locale="currentLocale" 
        :initial-date="initialDate"
        @date-selected="onDateSelected" 
      />
      
      <p v-if="selectedDate" class="selected-date-info">
        Выбранная дата: <strong>{{ selectedDate }}</strong>
      </p>
    </div>
  </div>
</template>

<script>
import VueCalendar from './components/VueCalendar.vue'
import './assets/styles/app.css'

export default {
  name: 'App',
  components: {
    VueCalendar
  },
  data() {
    return {
      selectedDate: null,
      currentLocale: 'ru',
      initialDate: null,
      initialDateInput: '',
      dateError: null
    }
  },
  methods: {
    onDateSelected(date) {
      this.selectedDate = date
      console.log('Выбрана дата:', date)
    },
    updateInitialDate() {
      this.dateError = null
      
      if (this.initialDateInput === '') {
        this.initialDate = null
        return
      }
      
      const dateRegex = /^\d{4}-\d{2}-\d{2}$/
      if (!dateRegex.test(this.initialDateInput)) {
        this.dateError = 'Неверный формат даты! Используйте формат: год-месяц-день (например: 2024-12-25)'
        this.initialDate = null
        return
      }
      
      const dateParts = this.initialDateInput.split('-')
      const year = parseInt(dateParts[0], 10)
      const month = parseInt(dateParts[1], 10) - 1
      const day = parseInt(dateParts[2], 10)
      
      const date = new Date(year, month, day)
      
      if (
        date.getFullYear() !== year ||
        date.getMonth() !== month ||
        date.getDate() !== day
      ) {
        this.dateError = 'Неверная дата! Проверьте правильность дня и месяца.'
        this.initialDate = null
        return
      }
      
      if (year < 1900 || year > 2100) {
        this.dateError = 'Год должен быть в диапазоне от 1900 до 2100'
        this.initialDate = null
        return
      }
      
      this.initialDate = this.initialDateInput
    },
    clearDateError() {
      if (this.dateError) {
        this.dateError = null
      }
    },
    clearInitialDate() {
      this.initialDateInput = ''
      this.initialDate = null
      this.dateError = null
    }
  }
}
</script>

