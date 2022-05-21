<template>
  <ul class="list-button">
    <li
      v-for="item in videoLists.length"
      :key="item"
      @click="handleItem(item - 1)"
    >
      {{ item }}
    </li>
    <li ref="printscreen" @click="printscreen" class="printscreen">截图</li>
  </ul>
  <video
    autoplay
    ref="video"
    :poster="poster"
    loop
    muted="muted"
    controls
    :src="targetVideo"
  ></video>
</template>

<script>
const videos = require.context("@/assets/video", false, /\.mp4$/);
const videoLists = [];
videos.keys().forEach((item) => {
  videoLists.push(videos(item));
});
export default {
  name: "App",
  data() {
    return {
      videoLists,
      targetVideo: videoLists[0],
      poster: "./assets/image/poster.jpg",
    };
  },
  computed: {
    video() {
      return this.$refs.video;
    },
  },
  mounted() {
    const { video } = this;
    video.play();
  },
  methods: {
    // 点击切换视频
    handleItem(item) {
      const { videoLists } = this;
      this.targetVideo = videoLists[item];
      this.poster = this.getposter();
    },
    // 获取video的截图src
    getposter() {
      const { video } = this;
      const canvas = document.createElement("canvas");
      const { width, height } = getComputedStyle(video);
      const [_w, _h] = [Number.parseInt(width), Number.parseInt(height)];
      canvas.width = _w;
      canvas.height = _h;
      canvas.getContext("2d").drawImage(video, 0, 0, _w, _h);
      return canvas.toDataURL("image/png");
    },
    // 截屏
    printscreen() {
      const img = this.getposter();
      const root = document.getElementById("app");
      const image = this.viewPrintscreen();
      image.src = img;
      this.poster = img;
      root.appendChild(image);
    },
    // 获取或者创建image
    viewPrintscreen() {
      let img = document.getElementById("printscreenImage");
      if (img) return img;
      img = document.createElement("img");
      img.id = "printscreenImage";
      return img;
    },
  },
};
</script>

<style scope>
video {
  width: 300px;
}
.list-button {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  height: 50px;
  text-align: center;
  line-height: 30px;
}
.list-button > li {
  width: 30px;
  height: 30px;
  border-radius: 3px;
  margin: 0 5px;
  background-color: #c2e7b0;
  color: #fff;
  cursor: pointer;
}
li.printscreen {
  width: auto;
  padding: 0 10px;
}
img {
  margin-left: 10px;
}
</style>
