<script setup lang="ts">
import { ref, watch } from 'vue'

// 🔥 API-ключ
const TOKEN_KEY = '49fbcce031394faa8ce141711251802'
const city = ref<string>('Москва')
const loading = ref(false)

// Интерфейс для данных о погоде
interface WeatherData {
  temp_c: number
  city: string
  condition: { text: string; icon: string }
}

const result = ref<WeatherData | null>(null)
// Функция для получения данных о погоде
const weatherFetch = async () => {
  try {
    loading.value = true
    const apiUrl = `https://api.weatherapi.com/v1/current.json?key=${TOKEN_KEY}&q=${city.value}&lang=ru`

    const response = await fetch(apiUrl)
    if (!response.ok) throw new Error(`Ошибка ${response.status}: ${response.statusText}`)

    const data = await response.json()
    if (!data.current) throw new Error('API не вернуло `current`')

    result.value = {
      temp_c: data.current.temp_c,
      city: data.location.name,
      condition: {
        text: data.current.condition.text,
        icon: data.current.condition.icon,
      },
    }
  } catch (error) {
    console.error('Ошибка при получении погоды:', error)
  } finally {
    loading.value = false
  }
}

// 🔄 Автоматический запрос при изменении `city`
watch(city, () => {
  weatherFetch()
})

// Запускаем запрос при загрузке страницы
weatherFetch()
</script>

<template>
  <div class="weather weather__container">
    <h2 class="weather__container-title">
      Погода в <span class="weather__container-city">{{ result?.city }}</span>
    </h2>
    <img v-if="result" :src="result.condition.icon" alt="Иконка погоды" />
    <p v-if="result">
      🌡 Температура:<span class="weather__container-temp"> {{ result.temp_c }}°C</span>
    </p>
    <p v-if="result">🌤 Описание: {{ result.condition.text }}</p>
    <p v-if="loading">Загружаю данные...</p>
    <div class="weather__inputs">
      <input v-model="city" placeholder="Введите город" />
      <button @click="weatherFetch">Обновить</button>
    </div>
    <p>Текущий город: {{ city }}</p>
  </div>
</template>

<style scoped lang="scss">
input {
  padding: 8px;
  margin: 10px 0;
  border-radius: 5px;
  border: 1px solid #ccc;
  width: 200px;
}

button {
  padding: 8px 15px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #369971;
}
.weather {
  &__container {
    // display: flex;
    margin: 0 auto;
    width: 400px;
    text-align: center;
    &-title {
      font-size: 35px;
    }
    &-city {
      color: #369971;
    }
    &-temp {
      color: #369971;
      font-size: 24px;
      padding: 5px;
    }
  }
  &__inputs {
    display: flex;
    gap: 10px;
    align-items: center;
    justify-content: center;
  }
}
</style>
