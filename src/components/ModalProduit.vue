<template>
  <div v-if="visible" class="modal-overlay">
    <div class="modal-content">
      <h2 v-if="mode === 'ajouter'">Ajouter un produit</h2>
      <h2 v-else-if="mode === 'modifier'">Modifier le produit</h2>
      <h2 v-else-if="mode === 'supprimer'">Supprimer le produit</h2>

      <!-- Formulaire ajout / modification -->
      <form v-if="mode !== 'supprimer'" @submit.prevent="soumettre">
        <div>
          <label>Nom :</label>
          <input v-model="form.nom" required />
        </div>
        <div>
          <label>Prix :</label>
          <input type="number" v-model.number="form.prix" required />
        </div>
        <div>
          <label>Quantité :</label>
          <input type="number" v-model.number="form.quantite" required />
        </div>
        <div class="modal-buttons">
          <button type="submit">
            {{ mode === "modifier" ? "Modifier" : "Ajouter" }}
          </button>
          <button type="button" @click="$emit('fermer')">Annuler</button>
        </div>
      </form>

      <!-- Confirmation suppression -->
      <div v-else>
        <p>
          Voulez-vous vraiment supprimer <strong>{{ produit?.nom }}</strong> ?
        </p>
        <div class="modal-buttons">
          <button @click="supprimer">Oui, supprimer</button>
          <button @click="$emit('fermer')">Annuler</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import axios from "axios";

const props = defineProps({
  visible: Boolean,
  mode: String,
  produit: Object,
});

const emit = defineEmits(["fermer"]);

const form = ref({
  nom: "",
  prix: 0,
  quantite: 0,
});

// Remplir le formulaire si mode modifier
watch(
  () => props.produit,
  (p) => {
    if (props.mode === "modifier" && p) {
      form.value = { nom: p.nom, prix: p.prix, quantite: p.quantite };
    } else {
      form.value = { nom: "", prix: 0, quantite: 0 };
    }
  },
  { immediate: true }
);

const soumettre = async () => {
  try {
    if (props.mode === "ajouter") {
      await axios.post(
        "https://pratique-django.onrender.com/api/produits/ajouter/",
        form.value
      );
    } else if (props.mode === "modifier") {
      await axios.put(
        `https://pratique-django.onrender.com/api/produits/modifier/${props.produit.id}/`,
        form.value
      );
    }
    emit("fermer");
  } catch (error) {
    console.error("Erreur lors de l’enregistrement :", error);
  }
};

const supprimer = async () => {
  try {
    await axios.delete(
      `https://pratique-django.onrender.com/api/produits/supprimer/${props.produit.id}/`
    );
    emit("fermer");
  } catch (error) {
    console.error("Erreur lors de la suppression :", error);
  }
};
</script>

<style>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 9999;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  background: white;
  padding: 24px;
  border-radius: 8px;
  width: 300px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
}

.modal-content h3 {
  margin-bottom: 16px;
}

.modal-content label {
  display: block;
  margin-top: 12px;
  font-weight: bold;
}

.modal-content input {
  width: 100%;
  padding: 6px;
  margin-top: 4px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.modal-buttons {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}

.modal-buttons button {
  padding: 6px 12px;
  margin-left: 10px;
  border: none;
  border-radius: 4px;
  background-color: #3498db;
  color: white;
  cursor: pointer;
}

.modal-buttons button:hover {
  background-color: #2980b9;
}
</style>
