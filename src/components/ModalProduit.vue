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
        <div class="modal-actions">
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
        <div class="modal-actions">
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
  mode: String, // 'ajouter', 'modifier', 'supprimer'
  produit: Object, // null ou { id, nom, prix, quantite }
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
        "https://pratique-django.onrender.com/api/produits/",
        form.value
      );
    } else if (props.mode === "modifier") {
      await axios.put(
        `https://pratique-django.onrender.com/api/produits/${props.produit.id}/`,
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
      `https://pratique-django.onrender.com/api/produits/${props.produit.id}/`
    );
    emit("fermer");
  } catch (error) {
    console.error("Erreur lors de la suppression :", error);
  }
};
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal-content {
  background: white;
  padding: 20px;
  border-radius: 10px;
  min-width: 300px;
}
.modal-content h2 {
  margin-top: 0;
}
.modal-actions {
  margin-top: 15px;
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}
</style>
