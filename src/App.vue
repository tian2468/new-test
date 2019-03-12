<template>
  <div id="app">
    <div id="nav">
      <!-- <span class="icon-back" style="display:block;font-size:1rem; color:#000;"></span> -->
    </div>
    <router-view/>
  </div>
</template>
<script>
export default {};
document.addEventListener("DOMContentLoaded", () => {
  let html = document.querySelector("html");
  let fontSize = window.innerWidth / 10;
  fontSize = fontSize > 50 ? 50 : fontSize;
  html.style.fontSize = fontSize + "px";
});
</script>
