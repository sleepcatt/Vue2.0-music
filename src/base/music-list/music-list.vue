<template>
  <keep-alive>
  <transition name="slide">
    <div class="musicList">
      <div class="listTitle">
        <span class="fanhui" @click="fanhui"></span>
      </div>
      <div class="musicImg" :style="{background: Img}" ref="bgImg"></div>
      <!-- 上划时遮住图片增大可视区域 -->
      <div class="bglayer" ref="bglayer"></div>
      <scroll class="musiclist"
              :data="musicList"
              :probe-type="probeType"
              :listen-scroll="listenScroll"
              @scroll="scroll"
              ref="musiclist">
        <song-list :songs="musicList" :Show="showPopup" :types="types"></song-list>
        <div v-show="!musicList.length" class="loading-container">
          <loading></loading>
        </div>
      </scroll>
    </div>
  </transition>
</keep-alive>
</template>
<script>
import SongList from "../song-list.vue";
import Scroll from "../scroll.vue";
import Loading from "../loading.vue";
import { handleSong } from "../song.js";
export default {
  components: {
    SongList,
    Scroll,
    Loading
  },
  computed: {
    Img() {
      return `url(${this.musicImg}) no-repeat`;
    }
  },
  created() {
    this.probeType = 3;
    this.listenScroll = true;
  },
  watch: {
    // 监听滚动事件
    scrollY(newY) {
      let YY = Math.floor(Math.max(-217, newY));
      let zIndex = 0;
      let scale = 1;
      this.$refs.bglayer.style["transform"] = `translate3D(0 ,${YY}px, 0)`;
      this.$refs.bglayer.style[
        "webkit-transform"
      ] = `translate3D(0 ,${YY}px, 0)`;
      // 下拉列表图片跟随缩放，计算缩放比例
      const precent = Math.abs(newY / 260);
      if (newY > 0) {
        scale = 1 + precent;
        zIndex = 10;
      }
      // 上划列表遮住图片
      if (newY < -217) {
        zIndex = 10;
        this.$refs.bgImg.style["padding-top"] = 0;
        this.$refs.bgImg.style["height"] = "45px";
      } else {
        this.$refs.bgImg.style["padding-top"] = "260px";
        this.$refs.bgImg.style["height"] = "0";
      }
      this.$refs.bgImg.style["transform"] = `scale(${scale})`;
      this.$refs.bgImg.style["webkit-transform"] = `scale(${scale})`;
      this.$refs.bgImg.style["z-index"] = zIndex;
    }
  },
  data() {
    return {
      scrollY: 0
    };
  },
  props: {
    musicList: {
      type: Array,
      default: []
    },
    musicImg: {
      type: String,
      default: ""
    },
    types: {
      type: String,
      default: ""
    },
    showPopup: {
      type: Boolean,
      default: true
    }
  },
  methods: {
    fanhui() {
      this.$router.push("/find");
    },
    scroll(pos) {
      this.scrollY = pos.y;
    }
  }
};
</script>

<style>
.musicList {
  position: fixed;
  width: 100%;
  bottom: 5px;
  top: 0;
  left: 0;
  overflow: hidden;
  z-index: 32;
  background: rgba(255, 255, 255, 1);
}
@media screen and (min-width: 769px) {
   .musicList {
        width: 560px;
        left: 50%;
        transform: translate3d(-50%, 0, 0)
    }
}
.fanhui {
  position: absolute;
  top: 0;
  left: 0;
  width: 30px;
  height: 30px;
  background: url(fanhui.svg) no-repeat rgba(7, 17, 27, 0.2);
  background-size: cover;
  margin: 10px 0 0 10px;
  z-index: 32;
}
.musicImg {
  position: relative;
  width: 100%;
  height: 0px;
  padding-top: 260px;
  background-size: cover !important;
  background-position: center !important;
}
.bglayer {
  width: 100%;
  height: 100%;
  background-color: #fff;
}
.musiclist {
  position: absolute;
  width: 100%;
  top: 260px;
  bottom: 10px;
  background-color: #fff;
}
.slide-enter-active,
.slide-leave-active {
  transition: all 0.3s;
}
.slide-enter,
.slide-leave-to {
  transform: translate3d(100%, 0, 0);
}
.loading-container {
  position: absolute;
  width: 100%;
  transform: translateY(-50%);
}
</style>





