<style lang="less">
.scroll-page {
  position: relative;
  top: 0;
  // transition: all 700ms ease 0s;
  transition-duration: 0ms;
  & > div {
    position: relative;
  }
}
</style>
<template>
  <div ref="scrollPage" class="scroll-page">
    <slot></slot>
  </div>
</template>
<script>
export default {
  data() {
    return {
      _height: 0,
      childs: [],
      scrollHeight: 0,
      _index: 0, //当前页面下标
      len: 0, //共有几个页面
    };
  },
  mounted() {
    window.addEventListener("resize", this.debounce(this.getDom, 50));
    this.getDom();
    window.addEventListener("mousewheel", this.scrollFunc, false);
  },
  methods: {
    scrollFunc(e) {
      this.$refs.scrollPage.style.transitionDuration = "1000ms";
      if ((e.wheelDelta && e.wheelDelta > 0) || (e.detail && e.detail > 0)) {
        this.scrollHeight += this._height;
        if (this.scrollHeight <= 0) {
          this.$refs.scrollPage.style.transform =
            "translate3d(0px," + this.scrollHeight + "px, 0px)";
        } else {
          this.scrollHeight = 0;
        }
      }
      if ((e.wheelDelta && e.wheelDelta < 0) || (e.detail && e.detail < 0)) {
        this.scrollHeight -= this._height;
        if (this.scrollHeight >= (-this.len + 1) * this._height) {
          this.$refs.scrollPage.style.transform =
            "translate3d(0px," + this.scrollHeight + "px, 0px)";
        } else {
          this.scrollHeight = (-this.len + 1) * this._height;
        }
      }
      this._index = Math.abs(this.scrollHeight / this._height);
      console.log(this._index);
      setTimeout(() => {
        this.$refs.scrollPage.style.transitionDuration = "0ms";
      }, 1000);
    },
    getDom() {
      this.$refs.scrollPage.style.transform = "translate3d(0px, 0px, 0px)";
      this._height = document.body.clientHeight;
      this.childs = this.$refs.scrollPage.children;
      Object.keys(this.childs).forEach((v, index) => {
        this.childs[index].setAttribute("data-index", index);
        this.childs[index].style.height = this._height + "px";
      });
      this.len = Object.keys(this.childs).length;
      if (this._index !== undefined) {
        this.scrollHeight = -this._index * this._height;
        console.log("this.scrollHeight", this.scrollHeight);
        this.$refs.scrollPage.style.transform =
          "translate3d(0px," + this.scrollHeight + "px, 0px)";
      }
    },
    
    debounce(fn, delay) {
      let timer = null;
      return function () {
        if (timer) {
          clearTimeout(timer);
        }
        timer = setTimeout(fn, delay);
      };
    },
  },
};
</script>
