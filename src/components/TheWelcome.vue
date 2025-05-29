<script setup>
import { ref, computed, onMounted } from "vue";
import axios from "axios";

const produits = ref([]);

// Appel API pour récupérer les produits
const fetchProduits = async () => {
  try {
    const response = await axios.get(
      "https://pratique-django.onrender.com/api/produits/"
    );
    produits.value = response.data;
  } catch (error) {
    console.error("Erreur lors du chargement des produits :", error);
  }
};

onMounted(fetchProduits);

// Calcul automatique des montants
const produitsAvecMontant = computed(() =>
  produits.value.map((produit) => ({
    ...produit,
    montant: produit.prix * produit.quantite,
  }))
);

// Totaux
const totalMontant = computed(() =>
  produitsAvecMontant.value.reduce((sum, p) => sum + p.montant, 0)
);
</script>

<template>
  <div class="p-4">
    <h1>Liste des Produits</h1>
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
