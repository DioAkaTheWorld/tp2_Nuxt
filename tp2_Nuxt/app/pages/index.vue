<template>
  <div style="padding: 10px;">
    <h1>Bières</h1>
    <p>Sélectionnez le type de bière que vous souhaitez consulter :</p>
    <ul style="font-size: 16px; line-height: 1.8;">
      <li>
        <NuxtLink to="/bieres-client?type=ale">Voir les bières Ale</NuxtLink>
      </li>
      <li>
        <NuxtLink to="/bieres-client?type=stouts">Voir les bières Stouts</NuxtLink>
      </li>
    </ul>
  </div>
</template>