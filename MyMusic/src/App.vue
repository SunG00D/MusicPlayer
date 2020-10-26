<template>
  <div id="box">
    <div class="buju">
      <center class="bg_color">
        <h2>{{ msg }}</h2>
        <audio
          :src="currentSrc"
          controls
          autoplay="autoplay"
          preload="auto"
          @play="musicPlay()"
          @pause="musicPause()"
          @ended="handleNext()"
        ></audio>
      </center>
      <ul>
        <li
          v-for="item in musicData"
          :key="item.id"
          v-bind:class="{ active: item.id == select }"
          @click="handleClick(item.songSrc, item.id)"
        >
          <div class="cover">
            <img v-bind:src="item.cover" height="100%" width="100%" />
          </div>
          <div>
            <h2>{{ item.id }}-{{ item.name }}</h2>
            <p>{{ item.author }}</p>
            <p>
              <span>{{ item.isPublish ? "出售" : "未出售" }}</span>
              <button @click.stop="item.isPublish ? (dialogVisible = true) : handleBuy()">购买</button>
            </p>
            <p style="color: red">{{ item.price | salePrice("价格：¥") }}</p>
          </div>
        </li>
      </ul>
      <el-dialog title="扫码支付" :visible.sync="dialogVisible" width="35%" :before-close="handleClose">
        <div class="qrcode"></div>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false" style="font-size: 16px;">取 消</el-button>
          <el-button type="primary" @click="dialogVisible = false" style="font-size: 16px;">确 定</el-button>
        </span>
      </el-dialog>
    </div>
    <div class="wz">
      <div id="app">
        <img
          v-bind:src="musicData[pic].cover"
          v-bind:class="{ Applogo: isPlaying, pause: isPaused }"
          height="500px"
          width="500px"
          style="margin-top: 100px;  border: solid 1px;"
        />
      </div>
      <br />
      <div style="padding-top: 20px">
        <center>
          <button @click="handlePrevious" id="nextbutton" style="float: left">上一首</button>
          <button @click="handleNext" id="nextbutton" style="float: right">下一首</button>
        </center>
      </div>
    </div>
  </div>
</template>

<script>
const musicData = [
  {
    id: 1,
    name: "Ed - Remember The Name",
    author: "Ed Sheeran",
    songSrc: "./static/Remember The Name.mp3",
    isActive: false,
    isPublish: false,
    price: 100,
    cover: "./static/rm2.jpg"
  },
  {
    id: 2,
    name: "HotDog - on the way now",
    author: "HotDog",
    songSrc: "./static/on the way now.mp3",
    isActive: true,
    isPublish: true,
    price: 20,
    cover: "./static/hotdog.jpg"
  },
  {
    id: 3,
    name: "李荣浩 - 年少有为",
    author: "李荣浩",
    songSrc: "./static/年少有为-李荣浩.mp3",
    isActive: true,
    isPublish: true,
    price: 30,
    cover: "./static/young.jpg"
  },
  {
    id: 4,
    name: "木小雅 - 最忆是南京",
    author: "木小雅",
    songSrc: "./static/最忆是南京.mp3",
    isActive: false,
    isPublish: false,
    price: 50,
    cover: "./static/nanjing.jpg"
  },
  {
    id: 5,
    name: "雨神 - 广东十年爱情故事",
    author: "雨神",
    songSrc: "./static/广东十年爱情故事.mp3",
    isActive: true,
    isPublish: true,
    price: 26,
    cover: "./static/1024.jpg"
  }
];

export default {
  beforeCreate() {
    document
      .querySelector("body")
      .setAttribute("style", "background-image: url(../static/01.jpg);");
  },
  data() {
    return {
      dialogVisible: false,
      flag: true,
      isPlaying: false,
      isPaused: false,
      pic: 0,
      select: 1,
      msg: "Music Player",
      musicData,
      currentSrc: "./static/Remember The Name.mp3",
      currentIndex: 1,
      active: true,
      cover: "./static/rm2.jpg"
    };
  },
  methods: {
    handleClick(src, index) {
      this.currentSrc = src;
      this.currentIndex = index;
      this.select = index;
      this.pic = this.select - 1;
    },
    handleNext() {
      this.currentIndex += 1;
      if (this.currentIndex <= 5) {
        this.currentSrc = this.musicData[this.currentIndex - 1].songSrc;
        this.select = this.currentIndex;
        this.pic = this.select - 1;
      } else {
        this.currentIndex = 1;
        this.currentSrc = this.musicData[this.currentIndex - 1].songSrc;
        this.select = this.currentIndex;
        this.pic = this.select - 1;
      }
    },
    handleBuy() {
      this.$alert("该唱片尚未发售", "提示", {
        confirmButtonText: "确定",
        type: "warning"
      });
    },
    musicPlay() {
      this.isPlaying = true;
      this.isPaused = false;
    },
    musicPause() {
      this.isPaused = true;
    },
    handleClose(done) {
      this.$confirm("确认取消支付？")
        .then(_ => {
          done();
        })
        .catch(_ => {});
    },
    handlePrevious() {
      this.currentIndex -= 1;
      if (this.currentIndex >= 1) {
        this.currentSrc = this.musicData[this.currentIndex - 1].songSrc;
        this.select = this.currentIndex;
        this.pic = this.select - 1;
      } else {
        this.currentIndex = 5;
        this.currentSrc = this.musicData[this.currentIndex - 1].songSrc;
        this.select = this.currentIndex;
        this.pic = this.select - 1;
      }
    }
  },
  filters: {
    salePrice: function(value, a) {
      return a + value;
    }
  }
};
</script>

</script>

<style scoped>
audio {
  width: 70%;
  height: 54px;
}
* {
  padding: 0;
  margin: 0;
}

ul {
  list-style: none;
}

ul li {
  margin: 10px 10px;
  padding: 8px 5px;
  border-radius: 3px;
}

ul li.active {
  background-color: #d2e2f3;
}

ul li h2 {
  color: darkred;
}

p.active {
  color: darkgoldenrod;
}

div.cover {
  float: left;
  border: 0px solid #000;
  width: 90px;
  height: 90px;
  margin-right: 30px;
  border-radius: 50%;
}

img {
  border-radius: 50%;
}

.buju {
  width: 40%;
  float: left;
}
.bg_color {
  border-radius: 10px;
  background-color: #ddd;
}
.wz {
  padding-right: 170px;

  float: right;
}

html,
body,
canvas {
  width: 100%;
  height: 100%;
  overflow: hidden;
  content-zooming: none;
  background-color: white;
  padding: 0;
  margin: 0;
}
#nextbutton {
  -webkit-appearance: none;
  -webkit-user-select: none;
  align-items: flex-start;
  background-color: rgb(255, 255, 255);
  background-image: none;
  border-bottom-color: rgb(204, 204, 204);
  border-bottom-left-radius: 4px;
  border-bottom-right-radius: 4px;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-image-outset: 0px;
  border-image-repeat: stretch;
  border-image-slice: 100%;
  border-image-source: none;
  border-image-width: 1;
  border-left-color: rgb(204, 204, 204);
  border-left-style: solid;
  border-left-width: 1px;
  border-right-color: rgb(204, 204, 204);
  border-right-style: solid;
  border-right-width: 1px;
  border-top-color: rgb(204, 204, 204);
  border-top-left-radius: 4px;
  border-top-right-radius: 4px;
  border-top-style: solid;
  border-top-width: 1px;
  box-sizing: border-box;
  color: rgb(51, 51, 51);
  cursor: pointer;
  display: inline-block;
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 14px;
  font-stretch: normal;
  font-style: normal;
  font-variant-caps: normal;
  font-weight: normal;
  height: 34px;
  letter-spacing: normal;
  line-height: 20px;
  margin-bottom: 0px;
  margin-left: 0px;
  margin-right: 0px;
  margin-top: 0px;
  overflow-x: visible;
  overflow-y: visible;
  padding-bottom: 6px;
  padding-left: 12px;
  padding-right: 12px;
  padding-top: 6px;
  text-align: center;
  text-indent: 0px;
  text-shadow: none;
  text-transform: none;
  touch-action: manipulation;
  vertical-align: middle;
  white-space: nowrap;
  width: 100px;
  word-spacing: 0px;
  writing-mode: horizontal-tb;
}

.Applogo {
}

@media (prefers-reduced-motion: no-preference) {
  .Applogo {
    animation: App-logo-spin infinite 20s linear;
  }
}

@keyframes App-logo-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.pause {
  animation-play-state: paused;
}

ul li:hover {
  background-color: rgb(226, 226, 226);
}

.qrcode {
  height: 600px;
  width: 100%;
  background-image: url(../static/qrcode.gif);
}
</style>

