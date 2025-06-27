<template>
  <div class="mb-3 ml-2 text-xl">收藏網址小工具</div>

  <input
    ref="titleInput"
    v-model.trim="title"
    type="text"
    class="input mb-2"
    placeholder="Please enter the title"
  />
  <input
    v-model.trim="url"
    type="text"
    class="input mr-2"
    placeholder="Please enter the url"
    @keyup.enter="addUrl"
  />
  <button
    class="btn btn-accent mr-2"
    @click="addUrl"
    :disabled="!title || !url"
  >
    Add
  </button>
  <button class="btn btn-outline btn-error" @click="clearUrls">
    Delete All
  </button>
  <hr class="my-4" />
  <div class="px-2">
    <div class="mb-3">已儲存的網址</div>
    <div v-for="url in urls" class="flex">
      <button
        class="btn btn-xs btn-outline btn-error mr-2 mb-3"
        @click="clearDevice(url.title)"
      >
        -
      </button>
      <a
        :href="url.link"
        target="_blank"
        class="text-stone-400 hover:text-stone-600"
      >
        {{ url.title || url.link }}
      </a>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
const urls = ref([]);
const title = ref('');
const url = ref('');
const titleInput = ref(null);

onMounted(() => {
  const storage = localStorage.getItem('url-storage');
  if (storage !== null) {
    const result = JSON.parse(storage);
    urls.value = result;
  }
});

const addUrl = () => {
  if (title.value === '' || url.value === '') return;
  const item = {
    id: crypto.randomUUID(),
    title: title.value,
    link: url.value,
  };
  urls.value.unshift(item);
  localStorage.setItem('url-storage', JSON.stringify(urls.value));
  title.value = '';
  url.value = '';
  titleInput.value?.focus();
};

const clearUrls = () => {
  urls.value = [];
  localStorage.removeItem('url-storage');
};

const clearDevice = (url) => {
  const deleteItem = urls.value.findIndex((item) => item.title === url);
  urls.value.splice(deleteItem, 1);
  localStorage.setItem('url-storage', JSON.stringify(urls.value));
};
</script>
