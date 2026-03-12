<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'

const route = useRoute()
const router = useRouter()

const bieres = ref([])
const priceMax = ref(route.query.pricemax || '')
const typeBiere = ref(route.query.type || 'ale')

const chargerBieres = async () => {
  try {
    bieres.value = []
    const data = await $fetch(`https://api.sampleapis.com/beers/${typeBiere.value}`)

    bieres.value = data
  } catch (error) {
    console.error("Erreur lors de la récupération des bières :", error)
  }
}

onMounted(() => {
  chargerBieres()
})

const bieresFiltrees = computed(() => {
  if (!priceMax.value) return bieres.value
  const max = parseFloat(priceMax.value)

  return bieres.value.filter(biere => {
    const priceNumber = parseFloat(biere.price.replace('$', ''))
    if (isNaN(priceNumber)) return false
    return priceNumber <= max
  })
})

watch(typeBiere, (nouveauType) => {
  router.push({ query: { ...route.query, type: nouveauType } })
  chargerBieres()
})

watch(priceMax, (nouvelleValeur) => {
  router.push({ query: { ...route.query, pricemax: nouvelleValeur || undefined } })
})
</script>

<template>
  <div style="padding: 20px;">
    <h1>Meilleures bières (Chargement Client)</h1>

    <div>
      <label for="typeBiere" style="margin-right: 10px;">Type :</label>
      <select v-model="typeBiere" id="typeBiere" style="padding: 5px; margin-right: 20px; border-radius: 4px;">
        <option value="ale">Ale</option>
        <option value="stouts">Stouts</option>
      </select>

      <label for="filtrePrix" style="margin-right: 10px;">Prix max ($) :</label>
      <input id="filtrePrix" type="number" v-model="priceMax" placeholder="Ex: 15" style="padding: 5px; border-radius: 4px; border: 1px solid #ccc;" />
    </div>

    <p v-if="bieres.length === 0">Chargement des bières en cours...</p>

    <ul v-else>
      <li v-for="biere in bieresFiltrees" :key="biere.id" style="margin-bottom: 10px;">
        <NuxtLink :to="`/bieres-client/${biere.id}?type=${typeBiere}`" style="text-decoration: none; color: #007bff;">
          <strong>{{ biere.name }}</strong>
        </NuxtLink>
        - {{ biere.price }}
      </li>
    </ul>

    <p v-if="bieres.length > 0 && bieresFiltrees.length === 0" style="color: red;">
      Aucune bière trouvée en dessous de ce prix.
    </p>

    <div style="margin-top: 20px;">
      <NuxtLink to="/">Retour à l'accueil</NuxtLink>
    </div>
  </div>
</template>