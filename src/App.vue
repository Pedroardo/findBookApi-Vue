<script setup>
import Navbar from "./components/Navbar.vue";
import SearchInput from "./components/SearchInput.vue";
import Card from "./components/Card.vue";
import Spinner from "./components/Spinner.vue";
import { ref } from "vue";

const books = ref(null);
const isLoading = ref(false);

async function search({ keyword }) {
  if (!keyword) return;
  const url = new URL(import.meta.env.VITE_BASE_URL);
  url.searchParams.set("q", keyword);

  isLoading.value = true;
  const res = await fetch(url);
  const data = await res.json();
  isLoading.value = false;
  books.value = { items: data.items, totalItems: data.totalItems };
}
</script>

<template>
  <Navbar />
  <section class="my-12">
    <div class="flex flex-col items-center justify-center">
      <h1 class="text-xl md:text-2xl font-semibold mb-6">
        Find the book you want
      </h1>
      <SearchInput @search="search" />
    </div>
  </section>
  <div class="w-full text-center" v-if="isLoading">
    <Spinner />
  </div>
  <section class="w-[90vw] md:w-[8-vw] mx-auto mb-8" v-else>
    <h1 class="text-xl font-semibold" v-if="books">
      {{ `${books.totalItems} Books Found` }}
    </h1>
    <div class="flex flex-col items-center" v-else>
      <img src="./assets/waiting-search.svg" class="w-36 h-36 mb-4" alt="" />
      <p>Waiting for Search</p>
    </div>
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-4" v-if="books">
      <Card v-for="{ volumeInfo, id } of books.items" :key="id">
        <template #header>
          <figure>
            <img
              :src="volumeInfo?.imageLinks?.thumbnail"
              alt="Book"
              class="w-full h-full md:w-48 object-cover"
            />
          </figure>
        </template>
        <template #content>
          <h3 class="font-bold md:text-lg">
            {{ volumeInfo?.title }}
            <p class="text-sm">
              Author : {{ volumeInfo?.authors?.toString() }}
            </p>
            <p class="text-sm">Publisher : {{ volumeInfo?.publisher }}</p>
            <p class="text-sm">Published : {{ volumeInfo?.publishedDate }}</p>
            <a
              :href="volumeInfo?.infoLink"
              target="_blank"
              rel="noopener"
              class="inline-block py-2 bg-zinc-900 px-3 text-white font-semibold rounded-md hover:bg-zinc-900/70"
            >
              More Info</a
            >
          </h3>
        </template>
      </Card>
    </div>
  </section>
</template>
