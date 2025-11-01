# Быстрый старт

## Шаг 1: Установка зависимостей

```bash
npm install
```

## Шаг 2: Запуск проекта

### Вариант 1: Использование Vite (рекомендуется)

```bash
npm run dev
```

Сервер разработки запустится на http://localhost:3000

### Вариант 2: Использование Vue CLI

```bash
npm run serve
```

## Шаг 3: Использование компонента

### Базовый пример

```vue
<template>
  <VueCalendar @date-selected="handleDateSelection" />
</template>

<script>
import VueCalendar from './components/VueCalendar.vue'

export default {
  components: {
    VueCalendar
  },
  methods: {
    handleDateSelection(date) {
      console.log('Выбрана дата:', date) // Формат: "2024-12-25"
    }
  }
}
</script>
```

### С параметрами

```vue
<VueCalendar 
  :initial-date="'2024-12-25'"
  :locale="'en'"
  @date-selected="handleDateSelection" 
/>
```

## Основные возможности

✅ **Переключение месяцев** - используйте кнопки "<" и ">" в заголовке календаря

✅ **Выбор даты** - кликните на любой день календаря

✅ **Событие выбора** - обработчик `@date-selected` получает дату в формате "год-месяц-день"

✅ **Начальная дата** - передайте `initial-date` для установки начальной даты

✅ **Смена языка** - измените проп `locale` на: `'ru'`, `'en'`, `'de'`, `'fr'`

## Примеры использования

Смотрите файл `src/App.vue` для полных примеров использования компонента.

