<template>
<div>
  <div class="content">
    <div class="left">
      <div class="qrcode-content">
        <div class="loading" v-if="showLoading">
          <img src="../assets/loading.gif" alt="">
        </div>
        <!-- 二维码插入 -->
          <div id="qrcode"></div> 
          <img class="qrcode-border" :src="this.$store.state.url" v-if="isImgShow">
      </div>
    </div>

    <div class="right">
      <div class="instruction">
        <div class="title">
          <h2>{{this.$store.state.name}}</h2>
          <div class="how">
            <span>提示：您可以通过上传黑白二维码或输入链接、文字方式生产ArtQRcode</span>
          </div>
        </div>
      </div>
      <div class="list" @mousewheel="mousemove">
        <list v-on:child-say="isClick"></list>
      </div>
      <textarea  cols="30" rows="10" v-model="text" placeholder="请输入文字或者链接"></textarea>
      <a @click="madeCode">点击生成艺术码</a>
      <a @click='update'>
        <!-- <input  type="button"> -->
        点击上传二维码图片</a>
      <div class="download-group">
        <a v-show="finishMade" @click="downloadImg">下载</a>
        <a v-show="!finishMade" class="unfinish-btn">下载</a>
      </div>
    </div>
  </div>

  <error :errorMsg="errorMsg" v-show="errShow"></error>
</div>
</template>

<script>
import  QRCode  from "../core/art-qrcode";
import list from "../components/list";
import error from "../components/error";

export default {
  data() {
    return {
      text: "",
      isImgShow: true,
      finishMade: false,
      showLoading: true,
      imgSrc: "",
      errorMsg: "错误",
      errShow: false,
      UIcomponents: {
        code1: {
          codeId: "678",
          name: "烘培工坊",
          save: "768552121",
          url: require("../assets/component1/main.jpg"),
          position: {
            top: 77,
            left: 77,
            width: 197,
            height: 197,
            bgWidth: 350,
            bgHeight: 500
          },
          path: {
            border: require("../assets/component1/border.png"),
            eye: require("../assets/component1/eye.png"),
            col2: require("../assets/component1/col2.png"),
            row3: require("../assets/component1/row3.png"),
            row4: require("../assets/component1/row4.png"),
            single: require("../assets/component1/single.png"),
            row2col2: require("../assets/component1/row2col2.png"),
            corner: require("../assets/component1/corner.png")
          }
        },
        code2: {
          codeId: "678",
          name: "简约方块拼凑风",
          save: "768552121",
          url: require("../assets/component2/main.jpg"),
          position: {
            top: 107,
            left: 99,
            width: 152,
            height: 152,
            bgWidth: 350,
            bgHeight: 500
          },
          path: {
            border: require("../assets/component2/border.jpg"),
            eye: require("../assets/component2/eye.png"),
            col2: require("../assets/component2/col2.png"),
            row3: require("../assets/component2/row3.png"),
            row4: require("../assets/component2/row4.png"),
            single: require("../assets/component2/single.png"),
            row2col2: require("../assets/component2/row2col2.png"),
            corner: require("../assets/component2/corner.png")
          }
        },
        code3: {
          codeId: "678",
          name: "pink girl",
          save: "768552121",
          url: require("../assets/component2/main.jpg"),
          position: {
            top: 80,
            left: 75,
            width: 195,
            height: 195,
            bgWidth: 350,
            bgHeight: 500
          },
          path: {
            border: require("../assets/component3/border.jpg"),
            eye: require("../assets/component3/eye.png"),
            col2: require("../assets/component3/col2.png"),
            row3: require("../assets/component3/row3.png"),
            single: require("../assets/component3/single.png"),
            row2col3: require("../assets/component3/row2col3.png"),
            row3col2: require("../assets/component3/row3col2.png"),
            row2col2: require("../assets/component3/row2col2.png"),
            corner: require("../assets/component3/corner.png")
          }
        }
      },
      UIscource: {}
    };
  },
  components: {
    list,
    error
  },
  mounted() {},
  created() {
    var self = this;
  },
  methods: {
    //判断是否点击了列表图片
    isClick: function() {
      this.isImgShow = true;
      this.finishMade = false;
      document.querySelector("#qrcode").innerHTML = "";
    },
    //上传图片
    update() {
      this.errorMsg = "该版本暂时不支持上传二维码";
      this.errShow = true;
      let self = this;
      setTimeout(() => {
        self.errShow = false;
      }, 2500);
    },
    //列表滑动事件
    mousemove() {
      // console.log(this);
    },

    //加载所有的素材文件后执行绘制操作
    madeCode() {
      if (this.text == "") {
        this.errorMsg = "请输入内容";
        this.errShow = true;
        let self = this;
        setTimeout(() => {
          self.errShow = false;
        }, 2500);
        return;
      }
      let qr = [], //存放所有 promise 的状态
        self = this,
        codeId = this.$store.state.codeId,
        UI = self.UIcomponents[codeId];

      self.isImgShow = false;
      self.showLoading = true;
      self.UIscource = {};
      self.UIscource["position"] = UI.position;
      //加载素材文件
      for (var key in UI.path) {
        let promise = new Promise(resolve => {
          let value = new Image();
          value.src = UI.path[key];
          self.UIscource[key] = value;
          value.onload = () => {
            resolve();
          };
        });
        qr.push(promise);
      }

      Promise.all(qr).then(() => {
        if (self.text) {
          let promise2 = new Promise(resolve => {
            self.drawQRCode();
            resolve();
          });
          promise2.then(() => {
            // self.showLoading=false;
          });
        }
      });
    },

    //绘制二维码
    drawQRCode() {
      var self = this;
      document.getElementById("qrcode").innerHTML = "";
      /**
       * QRCode的第一个参数是二维码要绘制到的dom
       */
      console.log("ok");
      let qrcode = new QRCode.QRCode(document.getElementById("qrcode"), {
        /**
         * text：二维码的信息
         */
        text: self.text,
        /**
         * width,height 是二维码的长宽
         * bgWidth,bgHeight 是整张图片的大小
         * top,left 是二维码距离整图的距离
         */
        width: self.UIscource.position.width,
        height: self.UIscource.position.height,
        bgWidth: self.UIscource.position.bgWidth,
        bgHeight: self.UIscource.position.bgHeight,
        top: self.UIscource.position.top,
        left: self.UIscource.position.left,
        /**
         * 对应每种遍历情况的填充图案
         */
        border: self.UIscource.border,
        eye: self.UIscource.eye,
        col2: self.UIscource.col2,
        row4: self.UIscource.row4,
        row3: self.UIscource.row3,
        row2col3: self.UIscource.row2col3,
        row3col2: self.UIscource.row3col2,
        single: self.UIscource.single,
        row2col2: self.UIscource.row2col2,
        corner: self.UIscource.corner
      });
      
      new Promise(resolve => {
        let a = qrcode.getImgUrl();
        resolve(a);
      }).then(a => {
        self.imgSrc = a;
        self.finishMade = true;
      });
    },
    //下载原图
    downloadImg: function() {
      let self = this,
        imgData = self.imgSrc;

      //IE浏览器下
      if (!!window.ActiveXObject || "ActiveXObject" in window) {
        this.errorMsg = "该浏览器暂不支持下载，您可以 右键-图片另存为";
        this.errShow = true;
        let self = this;
        setTimeout(() => {
          self.errShow = false;
        }, 2500);
      }

      //将mime-type改为image/octet-stream,强制让浏览器下载
      imgData = imgData.replace("image/png", "image/octet-stream");
      // 创建a标签
      var save_link = document.createElement("a");
      save_link.href = imgData;
      // 下载后的文件名设置
      save_link.download = "artQrcode_" + new Date().getTime() + "." + "png";
      let e = document.createEvent("MouseEvents");
      e.initMouseEvent("click");
      save_link.dispatchEvent(e);
    }
  }
};
</script>

<style lang="scss">
@import "../assets/scss/all.scss";
.content {
  width: 100%;
  max-width: p(1200px);
  margin: 0 auto;
  // display: flex;
  // justify-content: space-between;
  .right {
    // float: right;
    height: p(400px);
    padding: p(25px) p(25px) p(50px) p(75px);
    .instruction {
      text-align: left;
      .title {
        h2 {
          line-height: p(10px);
        }
        span {
          display: inline-block;
          margin: p(0px) p(10px) p(10px) 0;
        }
      }
      .how {
        width: 100%;
        height: p(30px);
        background: #ddd;
        border-radius: p(5px);
        margin: p(5px) 0;
        text-align: center;
        line-height: p(30px);
        span {
          color: #888;
          font-size: p(14px);
        }
      }
    }
    textarea {
      width: 100%;
      height: p(100px);
      resize: none;
      border: p(2px) solid #ddd;
      border-radius: p(5px);
      padding: p(10px);
      box-sizing: border-box;
      font-size: p(20px);
      // margin-top: p(10px);
    }
    a {
      display: inline-block;
      height: p(50px);
      line-height: p(50px);
      background: $bg;
      border-radius: 5px;
      margin: p(5px) auto;
      position: relative;
      &:first-of-type {
        width: 45%;
      }
      &:nth-of-type(2) {
        width: 35%;
      }

      &:hover {
        background: #333;
      }
      input[type="file"] {
        opacity: 0;
        position: absolute;
        top: 0;
        left: 0;
        height: 0;
        height: p(50px);
        width: 100%;
        cursor: pointer;
      }
    }
    .download-group {
      float: right;
      vertical-align: middle;
      width: 18%;
      height: p(50px);
      position: relative;
      a {
        position: absolute;
        top: 0;
        left: 0;
        height: p(50px);
        width: 100%;
      }
      .unfinish-btn {
        background: #999;
      }
    }
    .list {
      // overflow: scroll;
      width: p(700px);
      // overflow: scroll;
      // height: p(230px);
    }
  }

  .left {
    width: p(350px);
    // float: left;
    height: p(500px);
    background: $bg-light;
    border-radius: p(10px);
    padding: p(15px);
    margin: p(15px) auto;
    overflow: hidden;

    .qrcode-content {
      // width: p(350px);
      height: 100%;
      background: $bg;
      position: relative;
      background: $bg-light;
      margin: 0 auto;
      .loading {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        margin: auto;
        width: 50px;
        height: 50px;
        img {
          width: 50px;
          height: 50px;
        }
      }

      .qrcode-border {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        margin: auto;
        display: block;
        width: p(350px);
        height: auto;
      }
      #qrcode {
        position: absolute;
        top: 0;
        left: 0;
        // margin: p(-70px) 0 0 p(-70px);
        background-size: 50%;
        .qrcodeImg {
          // width: p(300px);
          // height: p(300px);
        }
      }
    }
  }
}
</style>
