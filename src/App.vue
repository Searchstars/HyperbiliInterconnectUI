<template>
  <p class="pagetitle">主页</p>
  <div class="statusbox">
    <img class="connectstatus" :src="ui_params.connected ? '/true.svg' : '/empty.svg'">
    <div class="statustext-container">
      <p class="statustext">{{ ui_params.connected ? '已连接设备' : '设备未连接' }}</p>
      <p class="statustext"
        style="font-size: 23px; font-weight: 500; margin-top: -15px; max-lines: 1; text-overflow: ellipsis;"
        v-if="ui_params.connected">{{
      ui_params.connected_device_name }}</p>
    </div>
  </div>
  <div class="infobox" style="margin-top: 20px; height: 251px">
    <div class="infotext" style="margin-top: 30px">小米运动健康连接状态</div>
    <div class="infotext infodetail">{{ ui_params.mifitness_connected ? '已连接' : '未连接' }}</div>
    <div class="infotext">设备权限申请状态</div>
    <div class="infotext infodetail">{{ ui_params.device_permission ? '已授权' : '未授权' }}</div>
    <div class="infotext">HyperBili同步器版本</div>
    <div class="infotext infodetail">{{ ui_params.interconnect_tool_version }}</div>
  </div>
  <div class="infobox" style="margin-top: 20px; height: 298px">
    <div style="margin-top: 20px; align-items: center; display: flex; flex-direction: row; margin-left: 30px;">
      <img src="/about.svg">
      <div style="font-size: 25px; font-weight: 600; margin-left: 15px; color: white">关于本应用</div>
    </div>
    <div class="infotext" style="margin-top: 10px">参与制作的人员</div>
    <div class="info-devname" style="margin-top: 10px">@RX79XT_</div>
    <div class="info-devname">@Searchstars</div>
    <div style="font-weight: 600; font-size: 22px; color: white; margin-top: 10px; margin-left: 30px">本应用永久免费且开源</div>
    <div style="font-weight: 600; font-size: 20px; color: white; margin-top: 4px; margin-left: 30px">并遵循GPL-V3协议</div>
    <div style="font-weight: 600; font-size: 20px; color: white; margin-top: 4px; margin-left: 30px">禁止倒卖或违反开源协议</div>
  </div>
  <div class="infobox" style="margin-top: 20px; height: 298px">
    <div style="margin-top: 20px; align-items: center; display: flex; flex-direction: row; margin-left: 30px;">
      <img src="/about.svg">
      <div style="font-size: 25px; font-weight: 600; margin-left: 15px; color: white">日志</div>
    </div>
    <div style="width: 100%; display: flex; justify-content: center;">
      <div class="log-box" ref="logBox">
        <div style="color: gray; font-size: 14px" v-for="(log, index) in logs" :key="index">{{ log }}</div>
      </div>
    </div>
  </div>
  <vue-qr v-if="ui_params.qrcode_key" :text="ui_params.qrcode_key" class="qr-code" @click="closeQR"></vue-qr>
</template>

<script scoped>
import vueQr from 'vue-qr/src/packages/vue-qr.vue'

export default {
  components: {
    vueQr
  },
  data() {
    return {
      ui_params: {
        connected: false,
        connected_device_name: "",
        mifitness_connected: false,
        device_permission: false,
        interconnect_tool_version: "unknown",
        qrcode_key: ""
      },
      logs: []
    }
  },
  methods: {
    scrollToBottom() {
      const logBox = this.$refs.logBox;
      logBox.scrollTop = logBox.scrollHeight;
    },
    closeQR(){
      androidlib.ClearQRKey()
    },
    createQRCode() {
      new QRCode(this.$refs.qrCodeUrl, {
        text: this.ui_params.qrcode_key,
        width: 150,
        height: 150,
        colorDark: '#000',
        colorLight: '#fff',
        correctLevel: QRCode.CorrectLevel.H
      });
    }
  },
  mounted(){
    setInterval(() => {
      this.ui_params = JSON.parse(androidlib.GetUIParams())
      this.logs = JSON.parse(androidlib.GetLogs())
      this.scrollToBottom()
      if(this.ui_params.qrcode_key){
        this.createQRCode()
      }
    }, 1000)
  }
}
</script>

<style>
.pagetitle {
  font-size: 50px;
  font-weight: 500;
}

.statusbox {
  background: #99DCF6;
  width: 100%;
  height: 120px;
  border-radius: 23px;
  display: flex;
  flex-direction: row;
  align-items: center;
}

.connectstatus {
  margin-left: 30px;
}

.statustext-container {
  flex-direction: column;
  margin-left: 25px;
}

.statustext {
  color: #1A2428;
  font-size: 20px;
  font-weight: 600;
}

.infobox {
  background: #2F434B;
  border-radius: 23px;
  width: 100%;
  display: flex;
  flex-direction: column;
}

.infotext {
  margin-left: 30px;
  margin-top: 16px;
  font-size: 20px;
  font-weight: 500;
  color: white;
}

.infodetail {
  margin-top: 2px;
  font-weight: 600;
}

.info-devname {
  font-size: 18px;
  font-weight: 500;
  margin-left: 30px;
  color: white;
  margin-top: 5px;
}

.log-box {
  width: 80%;
  height: 180px; /* 固定高度 */
  overflow-y: auto; /* 使元素出现滚动条 */
  scroll-behavior: smooth; /* 平滑滚动 */
  padding: 10px;
  margin-top: 15px;
  background-color: #1A2428;
  border-radius: 15px;
}

.qr-code {
  padding: 10px;
  margin-top: 20px;
  background-color: #fff;
  border: 1px solid red;
  position: fixed;  /* 使用 fixed 替代 absolute，使元素相对于视口而不是父元素定位 */
  top: 50%;         /* 设为视口的中央 */
  left: 50%;        /* 设为视口的中央 */
  transform: translate(-50%, -50%);  /* 使用 transform 平移元素，确保准确居中 */
}
</style>