<template>
  <transition name="slide-down">
    <div class="title-wrapper" v-show="isA">
      <div class="left">
        <span class="icon-back icon"></span>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <span class="icon-cart icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-person icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-more icon"></span>
        </div>
      </div>
    </div>
  </transition>
</template>
<style lang="scss"scoped>
@import "../assets/styles/gloabl.scss";
.title-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: px2rem(48);
  z-index: 101;
  display: flex;
  background: #fff;
  box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, 0.15);
  .left {
    flex: 0 0 px2rem(60);
    @include center;
  }
  .right {
    flex: 1;
    display: flex;
    justify-content: flex-end;
    .icon-wrapper {
      width: px2rem(40);
      @include center;
      .icon-cart {
        font-size: px2rem(22);
      }
    }
  }
}
</style>
<script>
export default {
  props: {
    isA: {
      type: Boolean,
      default: false
    }
  }
};
</script>

