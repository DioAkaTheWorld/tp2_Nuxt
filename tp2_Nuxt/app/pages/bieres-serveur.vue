<script setup lang="ts">
const { data: bieres, pending, error } = await useFetch('https://api.sampleapis.com/beers/ale')
</script>

<template>
  <div style="padding: 20px;">
    <h1>Meilleures bières (Côté Serveur)</h1>

    <p v-if="pending">Chargement sur le serveur...</p>
    <p v-else-if="error">Une erreur est survenue.</p>
    <ul v-else>
      <li v-for="biere in bieres" :key="biere.id">
        <NuxtLink :to="`/bieres-serveur/${biere.id}`">
          <strong>{{ biere.name }}</strong>
        </NuxtLink> - {{ biere.price }}
      </li>
    </ul>
    <NuxtLink to="/">Retour à l'accueil</NuxtLink>
  </div>
</template>