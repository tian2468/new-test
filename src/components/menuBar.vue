<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div class="menu-wrapper" :class="{'hide-box-shadow':isB||!isA}" v-show="isA">
        <div class="icon-wrapper">
          <span class="icon-menu icon" @click="showSetting(3)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-progress icon" @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-bright icon" @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-a icon" @click="showSetting(0)">A</span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="sizing-wapper" v-show="isB">
        <div class="setting-font-size" v-if="showTag===0">
          <div class="preview" :style="{fontSize:fontSizeList[0].fontSize +'px'}">A</div>
          <div class="select">
            <div
              class="select-wrapper"
              v-for="(item,index) in fontSizeList"
              :key="index"
              @click="setFontSize(item.fontSize)"
            >
              <div class="line"></div>
              <div class="pointer-wrapper">
                <div class="pointer" v-show="item.fontSize === defaultFontSize ">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div
            class="preview"
            :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize +'px'}"
          >A</div>
        </div>
        <div class="setting-theme" v-else-if="showTag===1">
          <div
            class="setting-theme-item"
            @click="setTheme(index)"
            v-for="(item,index) in themesList"
            :key="index"
          >
            <div class="preview" :style="{background:item.style.body.background}"></div>
            <div class="name" :class="{'selected': index === defaultTheme}">{{item.name}}</div>
          </div>
        </div>
        <div class="setting-progress" v-else-if="showTag===2">
          <div class="progress-wrapper">
            <input
              type="range"
              class="progress"
              max="100"
              min="0"
              step="1"
              @change="onProgressChange($event.target.value)"
              @input="onProgressInput($event.target.value)"
              :value="progress"
              :disabled="!bookAvailable"
              ref="progress"
            >
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable?progress+"%":"加载中..."}}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view
      :isC="isC"
      v-show="isC"
      :navigation="navigation"
      :bookAvailable="bookAvailable"
      @jumpTo="jumpTo"
    ></content-view>
    <transition name="fade">
      <div class="content-mask" v-show="isC" @click="hideContent"></div>
    </transition>
  </div>
</template>
<style lang="scss" scoped>
@import "../assets/styles/gloabl.scss";
.menu-bar {
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: px2rem(40);
    display: flex;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    z-index: 101;
    background: #fff;
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper {
      flex: 1;
      @include center;
      .icon-progress {
        font-size: px2rem(24);
      }
      .icon-bright {
        font-size: px2rem(24);
      }
    }
  }
  .sizing-wapper {
    position: absolute;
    bottom: px2rem(40);
    left: 0;
    width: 100%;
    height: px2rem(60);
    background: #fff;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    z-index: 100;
    .setting-font-size {
      display: flex;
      height: 100%;
      .preview {
        flex: 0 0 px2rem(40);
        @include center;
      }
      .select {
        flex: 1;
        display: flex;
        .select-wrapper {
          flex: 1;
          display: flex;
          align-content: center;
          @include center;
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #ccc;
          }
          .pointer-wrapper {
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid #ccc;
            position: relative;
            .pointer {
              position: absolute;
              top: px2rem(-7);
              left: px2rem(-11);
              width: px2rem(20);
              height: px2rem(20);
              border-radius: 50%;
              background: #fff;
              border: px2rem(1) solid #999;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, 0.15);
              @include center;
              .small-point {
                width: px2rem(5);
                height: px2rem(5);
                background: #000;
                border-radius: 50%;
              }
            }
          }
          &:first-child {
            // background: red;
            .line {
              &:first-child {
                border-top: none;
              }
            }
          }
          &:last-child {
            .line {
              &:last-child {
                border-top: none;
              }
            }
          }
        }
      }
    }
    .setting-theme {
      flex: 1;
      display: flex;
      height: 100%;
      .setting-theme-item {
        flex: 1;
        display: flex;
        flex-direction: column;
        @include center;
        .preview {
          width: px2rem(80);
          height: px2rem(20);
          border: px2rem(1) solid #ccc;
          box-shadow: 0 px2rem(2) px2rem(2) rgba(0, 0, 0, 0.15);
        }
        .name {
          color: #ccc;
          font-size: px2rem(16);
          margin-top: px2rem(10);
          &.selected {
            color: #000;
          }
        }
      }
    }
    .setting-progress {
      position: relative;
      width: 100%;
      height: 100%;
      .progress-wrapper {
        width: 100%;
        height: px2rem(30);
        @include center;
        padding: 0 px2rem(30);
        box-sizing: border-box;
        .progress {
          width: 100%;
          -webkit-appearance: none;
          height: px2rem(2);
          background: -webkit-linear-gradient(#000, #000) no-repeat, #ccc;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
          &::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: px2rem(20);
            height: px2rem(20);
            border-radius: 50%;
            background: #fff;
            box-shadow: 0 4px 4px rgba(0, 0, 0, 0.15);
            border: px2rem(1) solid #ddd;
          }
        }
      }
      .text-wrapper {
        width: 100%;
        height: px2rem(20);
        @include center;
        font-size: px2rem(16);
        color: #999;
      }
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
  }
}
</style>
<script>
import ContentView from "./content";
export default {
  components: {
    ContentView
  },
  data() {
    return {
      isB: false,
      showTag: 0,
      progress: 0,
      isC: false
    };
  },
  methods: {
    jumpTo(target) {
      this.$emit("jumpTo", target);
    },
    //隐藏遮罩
    hideContent() {
      this.isC = false;
      // console.log(this.$refs.isC);
    },
    //拖动进度条
    onProgressInput(progress) {
      this.progress = progress;
      // this.$refs.progress.style.backgroundSize = "${this.progress}% 100%";
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`;
    },
    //进度条调用
    onProgressChange(progress) {
      this.$emit("onProgressChange", progress);
    },
    setTheme(index) {
      this.$emit("setTheme", index);
    },
    //调整字体大小
    setFontSize(fontSize) {
      this.$emit("setFontSize", fontSize);
    },
    showSetting(tag) {
      this.showTag = tag;
      if (this.showTag === 3) {
        this.isB = false;
        this.isC = true;
      } else {
        this.isB = true;
      }
    },
    hideSetting() {
      this.isB = false;
    }
  },
  props: {
    isA: {
      type: Boolean,
      default: false
    },
    fontSizeList: Array,
    defaultFontSize: Number,
    defaultTheme: Number,
    themesList: Array,
    bookAvailable: Boolean,
    navigation: Object
  }
};
</script>


