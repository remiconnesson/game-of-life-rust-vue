<script setup lang="ts">
import { greet } from "game-of-life-rs";

const hello = "world";
</script>

<template>
  <h1>{{ hello }}</h1>
  <button @click="greet()">Greet</button>
</template>
