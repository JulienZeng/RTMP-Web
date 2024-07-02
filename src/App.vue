<template>
  <div class="common-layout">
    <el-container>
      <el-header style="padding: 0">
        <div class="center header">
          <div @click="toggleDark()">
            <button
              class="border-none w-full bg-transparent cursor-pointer"
              style="height: var(--ep-menu-item-height)"
            >
              <i inline-flex i="dark:ep-moon ep-sunny" />
            </button>
          </div>
        </div>
      </el-header>
      <el-main>
        <div class="center column">
          <div class="row">
            <span>直播地址：</span>
            <el-input
              v-model="liveUrl"
              placeholder="请输入直播地址"
              style="width: 300px"
            />
            <div style="margin: 0 10px 0 10px">
              <el-button @click="loadLive"> 播放 </el-button>
              <el-button type="primary" @click="destroy"> 摧毁 </el-button>
            </div>
          </div>
          <br />
          <div>
            <span>常用地址</span>
            <br /><br />
            <div class="usefulUrls">
              <div v-for="item in usefulUrls" :key="item.url">
                <el-button type="primary" plain @click="changeLiveUrl(item.url)"
                  ><span>{{ item.name }}</span></el-button
                >
              </div>
            </div>
          </div>
          <br />
          <div class="videoArea">
            <video id="videoElement" controls width="1280" height="720"></video>
          </div>
          <br />
          <div class="logArea">
            <el-input
              v-model="logger"
              style="width: 1280px"
              :rows="8"
              type="textarea"
              resize="none"
              disabled
            />
          </div>
        </div>
      </el-main>
    </el-container>
  </div>

  <el-dialog
    v-model="centerDialogVisible"
    title="Warning"
    width="500"
    align-center
  >
    <span>您的浏览器不支持播放。</span>
    <template #footer>
      <div class="dialog-footer">
        <el-button type="primary" @click="centerDialogVisible = false">
          确定
        </el-button>
      </div>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import FlvJs from "flv.js";
import { ref } from "vue";
import FormatDate from "./utils/FormatDate";
import { useDark, useToggle } from "@vueuse/core";
import cfg from "./config/config.yaml";

const isDark = useDark();
const toggleDark = useToggle(isDark);

// 直播地址
const liveUrl = ref("");

// 常用地址
const usefulUrls = ref(cfg.liveUrl);

// dialog
const centerDialogVisible = ref(false);

// flv player
const flvPlayer = ref({} as FlvJs.Player);

// log
const logger = ref("");

const loadLive = () => {
  if (!FlvJs.isSupported()) {
    centerDialogVisible.value = true;
  } else {
    const videoElement = document.getElementById(
      "videoElement"
    ) as HTMLVideoElement;
    flvPlayer.value = FlvJs.createPlayer(
      {
        type: "flv",
        url: liveUrl.value,
      },
      {
        isLive: true,
      }
    );
    flvPlayer.value.attachMediaElement(videoElement);
    flvPlayer.value.load();
    flvPlayer.value.play();
  }
};

FlvJs.LoggingControl.addLogListener(function (_type, str) {
  logger.value += `[${FormatDate(new Date(), "datetime", true)}] ${str}\n`;
});

const destroy = () => {
  flvPlayer.value.destroy();
};

const changeLiveUrl = (url: string) => {
  liveUrl.value = url;
};
</script>

<style scoped>
.videoArea {
  width: 1280px;
  height: 720px;
}
.column {
  display: flex;
  flex-direction: column;
}
.row {
  display: flex;
  flex-direction: row;
}
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
.logArea {
  width: 100%;
}
.usefulUrls {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
}
.usefulUrls > div {
  box-sizing: border-box;
}
.usefulUrls > div:nth-child(odd):last-child {
  grid-column: span 2;
  justify-self: center;
}
.header {
  height: 100%;
  justify-content: end;
}
</style>
