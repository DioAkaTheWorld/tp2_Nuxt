<script setup lang="ts">
import { ref, onMounted } from 'vue'

const favoris = ref([])

onMounted(() => {
  const favorisSauvegardes = localStorage.getItem('mesFavoris')
  if (favorisSauvegardes) {
    favoris.value = JSON.parse(favorisSauvegardes)
  }
})

const retirerFavori = (index) => {
  favoris.value.splice(index, 1)
  localStorage.setItem('mesFavoris', JSON.stringify(favoris.value))
}
</script>

<template>
  <div style="padding: 20px;">
    <h1>Mes Bières Favorites</h1>

    <div v-if="favoris.length === 0" style="margin-top: 20px; padding: 20px; background-color: #f8f9fa; border-radius: 8px;">
      <p>Vous n'avez pas encore ajouté de bières à vos favoris.</p>
    </div>

    <ul v-else style="margin-top: 20px; list-style-type: none; padding-left: 0;">
      <li v-for="(biere, index) in favoris" :key="index" style="margin-bottom: 15px; padding: 15px; border: 1px solid #eee; border-radius: 8px; display: flex; align-items: center; justify-content: space-between;">

        <div style="display: flex; align-items: center; gap: 15px;">
          <img v-if="biere.image" :src="biere.image" alt="Bière" referrerpolicy="no-referrer" style="width: 50px; height: 50px; object-fit: contain;" />

          <div>
            <NuxtLink :to="`/bieres-client/${biere.id}?type=${biere.type}`" style="color: #007bff; text-decoration: none; font-size: 1.1em;">
              <strong>{{ biere.name }}</strong>
            </NuxtLink>
            <br>
            <span style="color: #666; font-size: 0.9em;">Prix : {{ biere.price }} | Type : {{ biere.type }}</span>
          </div>
        </div>

        <button @click="retirerFavori(index)" style="cursor: pointer; padding: 8px 15px; background-color: #dc3545; color: white; border: none; border-radius: 4px;">
          Retirer
        </button>
      </li>
    </ul>

    <div style="margin-top: 30px;">
      <NuxtLink to="/bieres-client">⬅ Retour à la liste</NuxtLink>
    </div>
  </div>
</template>