<script setup lang="ts"></script>

<template>
  <header class="header">
    <RouterLink class="header__link" active-class="header__link-active" to="Weather"
      >Приложение погоды</RouterLink
    >

    <RouterLink class="header__link" active-class="header__link-active" to="/"
      >Лента новостей</RouterLink
    >
  </header>
  <main>
    <RouterView />
  </main>
</template>

<style scoped lang="scss">
.header {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
  background-color: #039;
  height: 80px;
  margin-bottom: 40px;
  &__link {
    font-size: 20px;
    font-family: 'roboto, Times New Roman', Times, serif;
    color: white;
    border-radius: 10px;
    text-decoration: none;
    width: 240px;
    padding: 15px;
    text-align: center;
    outline: none;
    border: none;
    position: relative;
    display: inline-block;
    border: 2px solid transparent;
    transition:
      background 0.38s ease-in-out,
      border 0.8s ease-in;
    div {
      width: 100%;
    }
    &:hover {
      background-color: rgba(28, 0, 153, 0.386);
    }
    &-active {
      border: 2px solid #839;
      width: 240px;
    }
  }
}
</style>
