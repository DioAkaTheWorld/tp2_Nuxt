<script setup lang="ts">
const route = useRoute()
const { data: biere, pending, error } = await useFetch(`https://api.sampleapis.com/beers/ale/${route.params.id}`)
</script>

<template>
  <div style="padding: 20px;">
    <NuxtLink to="/bieres-serveur">⬅ Retour à la liste (Serveur)</NuxtLink>

    <div v-if="pending" style="margin-top: 20px;">
      <p>Le serveur prépare la page de la bière...</p>
    </div>

    <div v-else-if="error" style="margin-top: 20px; color: red;">
      <p>Erreur : Impossible de trouver cette bière.</p>
    </div>

    <div v-else-if="biere" style="margin-top: 20px;">
      <h1>{{ biere.name }}</h1>
      <p><strong>Prix :</strong> {{ biere.price }}</p>

      <img v-if="biere.image" :src="biere.image" :alt="'Photo de ' + biere.name" style="max-width: 250px; border-radius: 8px; box-shadow: 0 4px 8px rgba(0,0,0,0.1);" />
      <p v-else>Aucune image disponible.</p>
    </div>
  </div>
</template>