<script setup lang="ts">
import { ref, onMounted } from 'vue'

const route = useRoute()
const biere = ref(null)
const isLoading = ref(true)

const typeBiere = route.query.type || 'ale'

onMounted(async () => {
  try {
    const bieres = await $fetch(`https://api.sampleapis.com/beers/${typeBiere}`)

    const idRecherche = Number(route.params.id)
    biere.value = bieres.find(b => b.id === idRecherche)
  } catch (error) {
    console.error("Erreur lors de la récupération :", error)
  } finally {
    isLoading.value = false
  }
})
</script>

<template>
  <div style="padding: 20px;">
    <NuxtLink :to="`/bieres-client?type=${typeBiere}`" style="color: #007bff; text-decoration: none;">⬅ Retour à la liste</NuxtLink>

    <div v-if="isLoading" style="margin-top: 20px;">
      <p>Chargement des détails de la bière...</p>
    </div>

    <div v-else-if="!biere" style="margin-top: 20px; color: red;">
      <p>Erreur : Aucune bière trouvée !</p>
    </div>

    <div v-else style="margin-top: 20px;">
      <h1>{{ biere.name }}</h1>
      <p><strong>Prix :</strong> {{ biere.price }}</p>

      <img v-if="biere.image" :src="biere.image" :alt="'Photo de ' + biere.name" referrerpolicy="no-referrer" style="max-width: 250px; border-radius: 8px; box-shadow: 0 4px 8px rgba(0,0,0,0.1);" />
      <p v-else>Aucune image disponible.</p>
    </div>
  </div>
</template>