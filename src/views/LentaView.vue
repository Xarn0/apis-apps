<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'

interface Post {
  title: string | null
  author: string | null
  category: string | null
}
const color = computed(() => {
  return '#' + Math.round(Math.random() * 1000)
})
const listPost = ref<Post[]>([])
const pagesLength = ref(10)
const listPostLength = computed(() => listPost.value.length)
const parseXML = (xmlString: string) => {
  const parser = new DOMParser()
  return parser.parseFromString(xmlString, 'application/xml')
}

const listPostReverce = computed(() => listPost.value.reverse())

const fetchAndParseXML = async () => {
  try {
    const proxyUrl = 'https://api.allorigins.win/raw?url='
    const targetUrl = 'https://lenta.ru/rss'
    const response = await fetch(proxyUrl + targetUrl)
    const responseData = await response.text()
    const xmlDoc = parseXML(responseData)

    let dataElements = xmlDoc.getElementsByTagName('channel')[0].getElementsByTagName('item')

    // Загружаем только нужное количество элементов
    const newPosts: Post[] = []
    for (let i = 0; i < pagesLength.value; i++) {
      if (dataElements[i]) {
        newPosts.push({
          title: dataElements[i].getElementsByTagName('title')[0]?.textContent || 'Без заголовка',
          author:
            dataElements[i].getElementsByTagName('author')[0]?.textContent || 'Неизвестный автор',
          category:
            dataElements[i].getElementsByTagName('category')[0]?.textContent || 'Без категории',
        })
      }
    }

    // Перезаписываем массив, а не добавляем в него
    listPost.value = newPosts
  } catch (error) {
    console.error('Ошибка загрузки XML:', error)
  }
}

// Обновление списка новостей (запрашивает больше элементов)
const updateLenta = async () => {
  pagesLength.value += 5
  await fetchAndParseXML()
}
watch(listPostReverce, () => {})
console.log(listPostReverce)
onMounted(fetchAndParseXML)
</script>

<template>
  <main>
    <div class="post__header">
      <button class="post__btn" @click="updateLenta">Обновить</button>
      <p>Количество записей: {{ listPostLength }}</p>
    </div>
    <ul class="post__list">
      <li v-for="(item, index) in listPostReverce" :key="index">
        <span class="post__item-number">{{ index + 1 }} </span>
        <strong class="post__item-title"> {{ item.title }}</strong> <br />
        <small>Автор: {{ item.author }}</small> <br />
        <em
          >Категория: <span class="post__item-category">{{ item.category }}</span>
        </em>
        <br />
        <hr />
      </li>
    </ul>
  </main>
</template>
<style lang="scss">
.post {
  &__header {
    display: flex;
    justify-content: space-between;
    margin: 0 auto;
    width: 400px;
    align-items: center;
    font-size: 20px;
  }
  &__btn {
    padding: 15px;
    border-radius: 20px;
    font-size: 20px;
    cursor: pointer;
  }
  &__list {
    list-style-type: none;
    display: flex;
    flex-direction: column;
    gap: 15px;

    margin-top: 20px;
  }
  &__item {
    &-number {
      font-size: 20px;
      color: greenyellow;
      padding-right: 10px;
    }
    &-title {
      font-size: 17px;
    }
    &-category {
      color: v-bind(color);
    }
  }
}
</style>
