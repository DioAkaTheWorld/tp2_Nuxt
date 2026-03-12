<script setup lang="ts">
import { ref, watch } from 'vue'

const route = useRoute()
const router = useRouter()

const typeBiere = ref(route.query.type || 'ale')
const priceMax = ref(route.query.pricemax || '')
const page = ref(Number(route.query.page) || 1)
const elementsParPage = 5

const { data: resultat, pending, error } = await useFetch(() => `https://api.sampleapis.com/beers/${typeBiere.value}`, {
  watch: [priceMax, page],

  transform: (toutesLesBieres) => {
    let filtrees = toutesLesBieres
    if (priceMax.value) {
      const max = parseFloat(priceMax.value)
      filtrees = filtrees.filter(biere => {
        const p = parseFloat(biere.price.replace('$', ''))
        return !isNaN(p) && p <= max
      })
    }

    const totalPages = Math.ceil(filtrees.length / elementsParPage)
    const debut = (page.value - 1) * elementsParPage
    const fin = debut + elementsParPage

    return {
      bieres: filtrees.slice(debut, fin),
      totalPages: totalPages
    }
  }
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

watch(typeBiere, () => { page.value = 1; mettreAJourURL() })
watch(priceMax, () => { page.value = 1; mettreAJourURL() })
watch(page, () => { mettreAJourURL() })
</script>

<template>
  <div style="padding: 20px;">
    <h1>Meilleures bières (Serveur optimisé)</h1>

    <div style="margin-bottom: 20px; padding: 15px; background-color: #f8f9fa; border-radius: 8px;">
      <label>Type :</label>
      <select v-model="typeBiere" style="margin-right: 20px; padding: 5px;">
        <option value="ale">Ales</option>
        <option value="stouts">Stouts</option>
      </select>

      <label>Prix max ($) :</label>
      <input type="number" v-model="priceMax" placeholder="Ex: 15" style="padding: 5px;" />
    </div>

    <p v-if="pending">Chargement depuis le serveur...</p>

    <ul v-else-if="resultat && resultat.bieres.length > 0">
      <li v-for="biere in resultat.bieres" :key="biere.id" style="margin-bottom: 10px;">
        <NuxtLink :to="`/bieres-serveur/${biere.id}?type=${typeBiere}`" style="color: #007bff; text-decoration: none;">
          <strong>{{ biere.name }}</strong>
        </NuxtLink> - {{ biere.price }}
      </li>
    </ul>

    <p v-else style="color: red;">Aucune bière trouvée.</p>

    <div v-if="resultat && resultat.totalPages > 1" style="margin-top: 20px; display: flex; gap: 10px; align-items: center;">
      <button :disabled="page <= 1" @click="page--">Précédent</button>
      <span>Page {{ page }} sur {{ resultat.totalPages }}</span>
      <button :disabled="page >= resultat.totalPages" @click="page++">Suivant </button>
    </div>

    <div style="margin-top: 30px;">
      <NuxtLink to="/">Retour à l'accueil</NuxtLink>
    </div>
  </div>
</template>