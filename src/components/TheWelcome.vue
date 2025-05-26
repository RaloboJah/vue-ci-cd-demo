<script setup>
import { ref, computed } from "vue";

// Exemple de produits
const produits = ref([
  { id: 1, nom: "Produit A", prix: 10, quantite: 2 },
  { id: 2, nom: "Produit B", prix: 20, quantite: 1 },
  { id: 3, nom: "Produit C", prix: 15, quantite: 3 },
]);

// Calcul automatique des montants
const produitsAvecMontant = computed(() =>
  produits.value.map((produit) => ({
    ...produit,
    montant: produit.prix * produit.quantite,
  }))
);

// Totaux (optionnel)
const totalMontant = computed(() =>
  produitsAvecMontant.value.reduce((sum, p) => sum + p.montant, 0)
);
</script>

<template>
  <div class="p-4">
    <table border="1" cellpadding="10">
      <thead>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Quantité</th>
          <th>Montant</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="produit in produitsAvecMontant" :key="produit.id">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prix }} €</td>
          <td>{{ produit.quantite }}</td>
          <td>{{ produit.montant }} €</td>
        </tr>
      </tbody>
    </table>

    <div style="margin-top: 20px; font-weight: bold">
      Total général : {{ totalMontant }} €
    </div>
  </div>
</template>

<style scoped>
h1 {
  margin-bottom: 20px;
}
table {
  width: 100%;
  text-align: left;
  border-collapse: collapse;
}
th,
td {
  padding: 8px 12px;
}
</style>
