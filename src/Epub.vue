<template>
  <div class="ebook">
    <title-bar :isA="isA"></title-bar>
    <div class="read-wrapper">
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
      <div id="read"></div>
    </div>
    <menu-bar
      :isA="isA"
      :fontSizeList="fontSizeList"
      :defaultFontSize="defaultFontSize"
      ref="menuBar"
      @setFontSize="setFontSize"
      :themesList="themesList"
      :defaultTheme="defaultTheme"
      @setTheme="setTheme"
      @onProgressChange="onProgressChange"
      :bookAvailable="bookAvailable"
      @jumpTo="jumpTo"
      :navigation="navigation"
    ></menu-bar>
  </div>
</template>
<script>
import titleBar from "../src/components/titleBar";
import menuBar from "../src/components/menuBar";
import Epub from "epubjs";
let DOWNLOAD_URL = "./2018_Book_AgileProcessesInSoftwareEngine.epub";
global.ePub = Epub;
export default {
  components: {
    titleBar,
    menuBar
  },
  data() {
    return {
      isA: false,
      fontSizeList: [
        {
          fontSize: 12
        },
        {
          fontSize: 14
        },
        {
          fontSize: 16
        },
        {
          fontSize: 18
        },
        {
          fontSize: 20
        },
        {
          fontSize: 22
        },
        {
          fontSize: 24
        }
      ],
      defaultFontSize: 16,
      themesList: [
        {
          name: "默认",
          style: {
            body: {
              color: "#000",
              background: "#fff"
            }
          }
        },
        {
          name: "护眼",
          style: {
            body: {
              color: "#000",
              background: "#ceeaba"
            }
          }
        },
        {
          name: "夜间",
          style: {
            body: {
              color: "#fff",
              background: "#24262e"
            }
          }
        },
        {
          name: "温暖",
          style: {
            body: {
              color: "#000",
              background: "#fedbb8"
            }
          }
        }
      ],
      defaultTheme: 0,
      bookAvailable: false,
      navigation: {}
    };
  },
  methods: {
    //根据链接跳转到相应位置
    jumpTo(href) {
      this.rendition.display(href);
      this.hideTitleAndMenu();
    },
    //隐藏title/nav/目录
    hideTitleAndMenu() {
      this.isA = false;
      //隐藏title
      this.$refs.menuBar.hideSetting();
      //隐藏目录
      this.$refs.menuBar.hideContent();
    },
    //改变进度方法
    onProgressChange(progress) {
      const percentage = progress / 100;
      const location =
        percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0;
      this.rendition.display(location);
    },
    //封装改变主题方法
    setTheme(index) {
      this.themes.select(this.themesList[index].name);
      this.defaultTheme = index;
    },
    //注册主题
    registerTheme() {
      this.themesList.forEach(theme => {
        this.themes.register(theme.name, theme.style);
        console.log(theme);
      });
    },
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize;
      if (this.themes) {
        this.themes.fontSize(fontSize + "px");
      }
    },
    //顶部AND底部导航显示隐藏
    toggleTitleAndMenu() {
      this.isA = !this.isA;
      if (!this.isA) {
        this.$refs.menuBar.hideSetting();
      }
    },
    //上一页
    prevPage() {
      //rendition.prev
      if (this.rendition) {
        this.rendition.prev();
        this.isA = false;
        this.$refs.menuBar.hideSetting();
      }
    },
    //下一页
    nextPage() {
      //rendition.next
      if (this.rendition) {
        this.rendition.next();
        this.isA = false;
        this.$refs.menuBar.hideSetting();
      }
    },
    //电子书结息和渲染
    showEpub() {
      //生成book
      this.book = new Epub(DOWNLOAD_URL);
      console.log(this.book);
      //生成Rendition
      this.rendition = this.book.renderTo("read", {
        width: window.innerWidth,
        height: window.innerHeight,
        method: "default"
      });
      console.log(window.innerWidth, window.innerHeight);
      //通过Rendition.display渲染电子书
      this.rendition.display();
      //获取Theme对象
      this.themes = this.rendition.themes;
      //设置默认字体
      this.setFontSize(this.defaultFontSize);
      // this.themes.register(name, styles);注册主体
      // this.themes.select(name);切换主题;
      this.registerTheme();
      //设置默认主题
      this.setTheme(this.defaultTheme);
      //获取location对象(默认不生成占用内存使用epub钩子函数构造)
      console.log(this.book.locations);
      this.book.ready
        .then(() => {
          this.navigation = this.book.navigation;
          console.log(this.navigation);
          return this.book.locations.generate();
        })
        .then(result => {
          // console.log(result);
          this.locations = this.book.locations;
          this.bookAvailable = true;
          // this.onProgressChange();
        });
    }
  },
  mounted() {
    this.showEpub();
  }
};
</script>
<style lang="scss" scoped>
@import "../src/assets/styles/gloabl.scss";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 100;
      display: flex;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>


