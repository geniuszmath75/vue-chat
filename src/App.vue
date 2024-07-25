<template>
  <div class="container">
    <div
      class="d-flex flex-column align-items-stretch flex-shrink-0 bg-body-tertiary"
    >
      <div
        class="d-flex align-items-center flex-shrink-0 p-3 link-body-emphasis text-decoration-none border-bottom"
      >
        <input class="fs-5 fw-semibold" v-model="username" />
      </div>
      <div class="list-group list-group-flush border-bottom scrollarea">
        <div
          class="list-group-item list-group-item-action py-3 lh-tight"
          v-for="message in messages"
          :key="message"
        >
          <div class="d-flex w-100 align-items-center justify-content-between">
            <strong class="mb-1">{{ message.username }}</strong>
          </div>
          <div class="col-10 mb-1 small">
            {{ message.message }}
          </div>
        </div>
      </div>
    </div>
    <form @submit.prevent="submit">
      <input
        class="form-control"
        placeholder="Write a message"
        v-model="message"
      />
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import Pusher from "pusher-js";

const username = ref("username");
const messages = ref([]);
const message = ref("");

const submit = async () => {
  try {
    await fetch("http://localhost:8000/api/messages", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        username: username.value,
        message: message.value,
      }),
    });
    message.value = "";
  } catch (err) {
    alert("Brak odpowiedzi od serwera.");
  }
};

onMounted(() => {
  const pusher = new Pusher("059cadb91ce869a467cf", {
    cluster: "eu",
  });

  const channel = pusher.subscribe("nodejs-chat");
  channel.bind("message", (data) => {
    messages.value.push(data);
  });
  console.log(messages.value);
});
</script>

<style scoped>
.scrollarea {
  min-height: 500px;
}
</style>
