<script setup lang="ts">
import { ref, onMounted } from 'vue'

const route = useRoute()
const biere = ref(null)

onMounted(async () => {
  try {
    const data = await $fetch(`https://api.sampleapis.com/beers/ale/${route.params.id}`)
    biere.value = data
  } catch (error) {
    console.error("Erreur lors de la récupération :", error)
  }
})
</script>

<template>
  <div style="padding: 20px;">
    <NuxtLink to="/bieres-client">⬅ Retour à la liste (Client)</NuxtLink>

    <div v-if="!biere" style="margin-top: 20px;">
      <p>Chargement des détails de la bière depuis le navigateur...</p>
    </div>

    <div v-else style="margin-top: 20px;">
      <h1>{{ biere.name }}</h1>
      <p><strong>Prix :</strong> {{ biere.price }}</p>

      <img v-if="biere.image" :src="biere.image" :alt="'Photo de ' + biere.name" style="max-width: 250px; border-radius: 8px; box-shadow: 0 4px 8px rgba(0,0,0,0.1);" />
      <p v-else>Aucune image disponible.</p>
    </div>
  </div>
</template>