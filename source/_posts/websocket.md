---
title: WebSocket
---

```html
<template>
  <el-dialog
    v-model="show"
    width="800"
    title="视频监控"
    draggable
    append-to-body
  >
    <div class="wrapper" v-loading="loading" element-loading-text="加载中...">
      <video
        style="width: 100%; height: 100%"
        ref="videoRef"
        controls
        autoplay
      ></video>
    </div>
  </el-dialog>
</template>

<script setup>
import { getDiviceTokenApi } from "@/api/smartDevice";

const $emit = defineEmits(["update:open", "confirm"]);
const props = defineProps({
  open: Boolean,
  currentRow: Object,
});

let show = computed({
  get: () => props.open,
  set: (val) => {
    $emit("update:open", val);
  },
});

watch(
  () => props.open,
  (val) => {
    val ? onInit() : onRemove();
  }
);

const { proxy } = getCurrentInstance();
const loading = ref(false);
const videoRef = ref(null);

let ws = null; // socket实例
let heartTimer = null; // 心跳定时器
let wsToken = null; // token
let isClosen = false; // 是否主动关闭

const handleOpen = () => {
  console.log("ws链接成功");
  // 发送登录
  ws.send(
    JSON.stringify({
      act: "ma_login",
      access_token: wsToken,
      user_name: props.currentRow.adminUserName,
    })
  );

  // 发送心跳包
  heartTimer = setInterval(() => {
    if (ws.readyState === WebSocket.OPEN) {
      ws.send(JSON.stringify({ act: "ma_get_active_devices" }));
    } else {
      clearInterval(heartTimer);
    }
  }, 1000 * 25);
};

const handleMessage = (e) => {
  const data = JSON.parse(e.data);

  // 等待登录成功后再发送订阅请求
  if (data.cmd == "ma_login" && data.status) {
    // 发送订阅请求
    ws.send(
      JSON.stringify({
        act: "ma_open_rtsp",
        device_id: props.currentRow.catNo,
      })
    );
  }

  if (data.cmd !== "ma_open_rtsp") return;

  new ZLMRTCClient.Endpoint({
    element: videoRef.value,
    debug: false,
    zlmsdpUrl: data.webrtc_url,
    simulcast: true,
    useCamera: true,
    audioEnable: true,
    videoEnable: true,
    recvOnly: true,
    resolution: { w: 1280, h: 720 },
  });

  loading.value = false;
};

const handleError = (e) => {
  console.log("ws链接出错", e);
  proxy.$modal.msgError("链接报错，请重新尝试");
};

const handleClose = (e) => {
  if (!isClosen) proxy.$modal.msgError("链接异常，请重新尝试");
  console.log("ws链接关闭", e);
};

const onInit = async () => {
  try {
    isClosen = false;
    loading.value = true;

    const { data: token } = await getDiviceTokenApi(props.currentRow.catNo);
    wsToken = token;

    ws = new WebSocket("wss://...");
    ws.onopen = handleOpen;
    ws.onmessage = handleMessage;
    ws.onerror = handleError;
    ws.onclose = handleClose;
  } catch (err) {
    console.log(err);
  }
};

const onRemove = () => {
  // 停止推流
  ws?.send(
    JSON.stringify({
      act: "ma_stop_rtsp",
      device_id: props.currentRow.catNo,
    })
  );

  videoRef.value?.pause(); // 暂停播放
  clearInterval(heartTimer);
  isClosen = true;
  ws?.close(); // 关闭连接
  ws = null;
};

onUnmounted(() => {
  onRemove();
});
</script>

<style lang="scss" scoped>
.wrapper {
  height: 430px;
}
</style>
```
