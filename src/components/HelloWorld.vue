<template>
  <div>
    <h1>WebSocket Chat</h1>
    <div v-for="message in messages" :key="message">{{ message }}</div>

    <input v-model="newMessage" @keyup.enter="sendMessage" placeholder="Type your message" />
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { io } from "socket.io-client";

// ตั้งค่า WebSocket connection
const socket = io("http://localhost:8000", {
  transports: ["websocket"],  // ใช้ WebSocket สำหรับการเชื่อมต่อที่เสถียร
});

const messages = ref([]);
const newMessage = ref("");

// เมื่อ component ถูก mount ให้เชื่อมต่อ WebSocket
onMounted(() => {
  socket.on("message", (data) => {
    messages.value.push(data);
  });
});

// เมื่อ component ถูกทำลาย ให้ปิดการเชื่อมต่อ WebSocket
onBeforeUnmount(() => {
  socket.disconnect();
});

// ฟังก์ชันสำหรับส่งข้อความ
const sendMessage = () => {
  if (newMessage.value) {
    socket.emit("message", newMessage.value);
    newMessage.value = "";
  }
};
</script>

<style scoped>
input {
  margin-top: 10px;
  padding: 10px;
  width: 100%;
}
</style>
