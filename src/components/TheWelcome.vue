<script setup>
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import ModalProduit from "./ModalProduit.vue";
import "@fortawesome/fontawesome-free/css/all.css";
import "@fortawesome/fontawesome-free/js/all.js";

const produits = ref([]);
const modalVisible = ref(false);
const modalMode = ref("ajouter");
const produitActuel = ref(null);

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

const produitsAvecMontant = computed(() =>
  produits.value.map((produit) => ({
    ...produit,
    montant: produit.prix * produit.quantite,
  }))
);

const totalMontant = computed(() =>
  produitsAvecMontant.value.reduce((sum, p) => sum + p.montant, 0)
);

const ouvrirModal = (mode, produit = null) => {
  modalMode.value = mode;
  produitActuel.value = produit;
  modalVisible.value = true;
};

const fermerModal = () => {
  modalVisible.value = false;
  fetchProduits();
};
</script>

<template>
  <div class="p-4">
    <h1>Liste des Produits</h1>
    <button @click="ouvrirModal('ajouter')">Ajouter un produit</button>

    <table border="1" cellpadding="10">
      <thead>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Quantité</th>
          <th>Montant</th>
          <th class="actions-col">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="produit in produitsAvecMontant" :key="produit.id">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prix }} €</td>
          <td>{{ produit.quantite }}</td>
          <td>{{ produit.montant }} €</td>
          <td>
            <button
              class="icon-button"
              @click="ouvrirModal('modifier', produit)"
            >
              <i class="fas fa-edit" title="Modifier"></i>
            </button>
            <button
              class="icon-button"
              @click="ouvrirModal('supprimer', produit)"
            >
              <i class="fas fa-trash-alt" title="Supprimer"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <div style="margin-top: 20px; font-weight: bold">
      Total général : {{ totalMontant }} €
    </div>

    <!-- Composant modal -->
    <ModalProduit
      :visible="modalVisible"
      :mode="modalMode"
      :produit="produitActuel"
      @fermer="fermerModal"
    />
  </div>
</template>

<style scoped>
h1 {
  margin-bottom: 20px;
}

button {
  margin-right: 5px;
  padding: 6px 10px;
  border: none;
  color: white;
  cursor: pointer;
  border-radius: 4px;
}

button:hover {
  background-color: #2980b9;
}

table {
  width: 100%;
  text-align: left;
  border-collapse: collapse;
  margin-top: 20px;
  border: 1px solid #ccc; /* Bordure fine autour de la table */
}

th,
td {
  padding: 10px;
  border: 1px solid #ccc; /* Bordure fine entre cellules */
}

th {
  background-color: #f4f4f4;
  font-weight: bold;
}

td {
  background-color: white;
}

th:last-child {
  text-align: center;
}

td:last-child {
  text-align: center;
}

td:last-child button {
  margin: 0 5px;
}
</style>
