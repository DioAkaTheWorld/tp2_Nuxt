<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'

const route = useRoute()
const router = useRouter()

const bieres = ref([])
const typeBiere = ref(route.query.type || 'ale')
const priceMax = ref(route.query.pricemax || '')
const page = ref(Number(route.query.page) || 1)
const elementsParPage = 5

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
    const p = parseFloat(biere.price.replace('$', ''))
    return !isNaN(p) && p <= max
  })
})

const totalPages = computed(() => Math.ceil(bieresFiltrees.value.length / elementsParPage))

const bieresAffichees = computed(() => {
  const debut = (page.value - 1) * elementsParPage
  return bieresFiltrees.value.slice(debut, debut + elementsParPage)
})

const mettreAJourURL = () => {
  router.push({
    query: {
      type: typeBiere.value,
      pricemax: priceMax.value || undefined,
      page: page.value > 1 ? page.value : undefined
    }
  })
}

watch(typeBiere, () => { page.value = 1; chargerBieres(); mettreAJourURL() })
watch(priceMax, () => { page.value = 1; mettreAJourURL() })
watch(page, () => { mettreAJourURL() })
</script>

<template>
  <div style="padding: 20px;">
    <h1>Meilleures bières (Client)</h1>

    <div style="margin-bottom: 20px; padding: 15px; border-radius: 8px;">
      <label>Type :</label>
      <select v-model="typeBiere" style="margin-right: 20px; padding: 5px;">
        <option value="ale">Ales</option>
        <option value="stouts">Stouts</option>
      </select>

      <label>Prix max ($) :</label>
      <input type="number" v-model="priceMax" placeholder="Ex: 15" style="padding: 5px;" />
    </div>

    <p v-if="bieres.length === 0">Chargement depuis le navigateur...</p>

    <ul v-else-if="bieresAffichees.length > 0">
      <li v-for="biere in bieresAffichees" :key="biere.id" style="margin-bottom: 10px;">
        <NuxtLink :to="`/bieres-client/${biere.id}?type=${typeBiere}`" style="color: #007bff; text-decoration: none;">
          <strong>{{ biere.name }}</strong>
        </NuxtLink> - {{ biere.price }}
      </li>
    </ul>

    <p v-else>Aucune bière trouvée.</p>

    <div v-if="totalPages > 1" style="margin-top: 20px; display: flex; gap: 10px; align-items: center;">
      <button :disabled="page <= 1" @click="page--">Précédent</button>
      <span>Page {{ page }} sur {{ totalPages }}</span>
      <button :disabled="page >= totalPages" @click="page++">Suivant</button>
    </div>

    <div style="margin-top: 30px;">
      <NuxtLink to="/">Retour à l'accueil</NuxtLink>
    </div>
  </div>
</template>