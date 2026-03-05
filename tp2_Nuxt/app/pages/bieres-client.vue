<script setup lang="ts">
import { ref, onMounted } from 'vue'

const bieres = ref([])

onMounted(async () => {
  try {
    const data = await $fetch('https://api.sampleapis.com/beers/ale')
    bieres.value = data
  } catch (error) {
    console.error("Erreur lors de la récupération des bières :", error)
  }
})
</script>

<template>
  <div style="padding: 20px;">
    <h1>Meilleures bières (Chargement Client)</h1>
    <p v-if="bieres.length === 0">Chargement des bières en cours...</p>
    <ul v-else>
      <li v-for="biere in bieres" :key="biere.id">
        <strong>{{ biere.name }}</strong> - {{ biere.price }}
      </li>
    </ul>
    <NuxtLink to="/">Retour à l'accueil</NuxtLink>
  </div>
</template>