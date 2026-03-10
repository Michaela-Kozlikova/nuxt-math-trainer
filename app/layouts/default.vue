<template>
  <link href="https://fonts.googleapis.com/css2?family=Hanalei+Fill&family=Indie+Flower&family=Mansalva&display=swap" rel="stylesheet"/>
  <div class="layout-container">
    <slot />
    <div class="footer">
      <p>&copy; Vytvořeno s ❤ by Misha | 2026</p>
    </div>
  </div>
</template>

<style>
.layout-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.footer {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 14px;
  font-family: 'Roboto', sans-serif;
  background-color: rgb(109, 83, 214);
  color: white;
}
</style>
